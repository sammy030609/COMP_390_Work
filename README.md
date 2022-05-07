```
import json
import pandas as pd
import urllib3

client = urllib3.PoolManager()
season = "2018-19"

frame = extract_data(client, player_stats_url(season))

def extract_data(http_client, url):
    r = http_client.request('GET', url, headers=header_data)
    resp = json.loads(r.data)                           
    results = resp['resultSets'][0]                     
    headers = results['headers']                        
    rows = results['rowSet']                            
    frame = pd.DataFrame(rows)                              
    frame.columns = headers                                 
    return frame
    
frame.to_csv("stats_nba_player_data_{0}.csv".format(season), index=False)

```

```
import bs4
import urllib3
import pandas as pd

http = urllib3.PoolManager()

season = '2019'

columns = []
rows = []

r = http.request('GET', player_totals_page(season))
soup = bs4.BeautifulSoup(r.data, 'html')     
f = soup.find_all("table")   
if len(f) > 0:  
    columns = extract_column_names(f[0])
    rows = rows + extract_rows(f[0]) 

frame = pd.DataFrame(rows)   

def extract_column_names(table):
    columns = [col["aria-label"] for col in table.find_all("thead")[0].find_all("th")][1:]
    columns.append("id")
    return columns
    
def extract_rows(table):
    rows = table.find_all("tbody")[0].find_all("tr")
    parsed_rows = []
    for r in rows:
        parsed = parse_row(r)
        if len(parsed) > 0:
            parsed_rows.append(parsed)
    return parsed_rows

def parse_row(row):
    other_data = row.find_all("td")
    if len(other_data) == 0:
        return []
    id = other_data[0].find_all("a")[0]["href"].replace("/players/", "").replace(".html","").split("/")[-1]
    row_data = [td.string for td in other_data]
    row_data.append(id)
    return row_data
    
frame.columns = columns
frame.to_csv("basketball_reference_totals_{0}.csv".format(season), index=False)


```

```
import pandas as pd
from fuzzywuzzy import fuzz

season = "2019"

bbref_data = pd.read_csv("basketball_reference_totals_{}.csv".format(season))

nba_data = pd.read_csv("stats_nba_player_data_{}.csv".format(convert_bbref_season_to_nba_season(season)))

nba_data["PLAYER_ID"] = nba_data["PLAYER_ID"].astype(str)

def convert_bbref_season_to_nba_season(season):
    year = int(season)
    last_year = year - 1
    last_two = season[-2:]
    return "{0}-{1}".format(last_year, last_two)
    
bbref_base_data = bbref_data[["Player", "id", "Team", "Field Goal Attempts", "Total Rebounds", "Assists"]].groupby(
    by="id").apply(deduplicate_traded_players)
    
def deduplicate_traded_players(group):
    if len(group) > 1:
        return group[group["Team"] == "TOT"]
    return group
    
nba_base_data = nba_data[["PLAYER_ID", "PLAYER_NAME", "FGA", "REB", "AST"]]

name_matches = bbref_base_data.merge(nba_base_data,
                                     left_on=["Player", "Field Goal Attempts", "Total Rebounds", "Assists"],
                                     right_on=["PLAYER_NAME", "FGA", "REB", "AST"], how="outer")
                                     
name_matches_ids = name_matches.dropna()
name_matches_ids = name_matches_ids[["Player", "id", "PLAYER_NAME", "PLAYER_ID"]]
name_matches_ids.columns = ["bbref_name", "bbref_id", "nba_name", "nba_id"]

non_matches = name_matches[name_matches.isnull().any(axis=1)]

bbref_non_matches = non_matches[["Player", "id", "Field Goal Attempts", "Total Rebounds", "Assists"]].dropna()

nba_non_matches = non_matches[["PLAYER_NAME", "PLAYER_ID", "FGA", "REB", "AST"]].dropna()

possible_matches = bbref_non_matches.merge(nba_non_matches,
                                     left_on=["Field Goal Attempts", "Total Rebounds", "Assists"],
                                     right_on=["FGA", "REB", "AST"], how="outer").apply(check_names_fuzzy_match, axis=1)

print( possible_matches[possible_matches["name_match"] != True].head(10))

fuzzy_matches = possible_matches[possible_matches["name_match"]][["Player", "id", "PLAYER_NAME", "PLAYER_ID"]]

fuzzy_matches.columns = ["bbref_name", "bbref_id", "nba_name", "nba_id"]

def check_names_fuzzy_match(row):
    row["name_match"] = fuzz.partial_ratio(row["Player"], row["PLAYER_NAME"]) > 60
    return row
    
all_matches = fuzzy_matches.append(name_matches_ids)

all_matches.to_csv("nba_id_matches_{}.csv".format(season))

```