---
title: "Top 10 soccer players in the FIFA Video Games"
author: "Jiehwan Yang (양지환)"
date: "2016년 9월 4일"
output: html_document
---





## 데이터 로딩...
## Loading data

필요한 패키지를 설치 및 로딩하고 sqlite에 연결한 뒤, 관심있는 데이터를 로딩한다. 경우에 따라 "\" 백슬래쉬를 두번 넣어야 할 수도 있음. 그리고 sqlite를 처음 사용하는 경우, 사용중인 디바이스에 sqlite를 설치해야할 수도 있습니다.

Install and load required packages, connect to sqlite, then load whatever data you are interested in. Note that you might have to put "\" twice for correct syntax. Also, if this is your first time using sqlite, you may have to install it.


```r
library(webshot)
library(RSQLite)
library(dplyr)
library(DT)

setwd("E:\\양지환\\soccer")
sqlite<- dbDriver("SQLite")
conn<- dbConnect(sqlite,"database.sqlite")

# list available tables
dbListTables(conn)
```

```
## [1] "Country"         "League"          "Match"           "Player"         
## [5] "Player_Stats"    "Team"            "sqlite_sequence"
```

```r
player<- tbl_df(dbGetQuery(conn,"select * from Player"))
player_stats<- tbl_df(dbGetQuery(conn,"select * from Player_Stats"))

# let's see columns in each table
str(player)
```

```
## Classes 'tbl_df', 'tbl' and 'data.frame':	11060 obs. of  7 variables:
##  $ id                : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ player_api_id     : int  505942 155782 162549 30572 23780 27316 564793 30895 528212 101042 ...
##  $ player_name       : chr  "Aaron Appindangoye" "Aaron Cresswell" "Aaron Doran" "Aaron Galindo" ...
##  $ player_fifa_api_id: int  218353 189615 186170 140161 17725 158138 221280 152747 206592 188621 ...
##  $ birthday          : chr  "1992-02-29 00:00:00" "1989-12-15 00:00:00" "1991-05-13 00:00:00" "1982-05-08 00:00:00" ...
##  $ height            : num  183 170 170 183 183 ...
##  $ weight            : int  187 146 163 198 154 161 146 139 181 170 ...
```

```r
str(player_stats)
```

```
## Classes 'tbl_df', 'tbl' and 'data.frame':	183253 obs. of  42 variables:
##  $ id                 : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ player_fifa_api_id : int  218353 218353 218353 218353 218353 189615 189615 189615 189615 189615 ...
##  $ player_api_id      : int  505942 505942 505942 505942 505942 155782 155782 155782 155782 155782 ...
##  $ date_stat          : chr  "2016-02-18 00:00:00" "2015-11-19 00:00:00" "2015-09-21 00:00:00" "2015-03-20 00:00:00" ...
##  $ overall_rating     : int  67 67 62 61 61 74 74 73 73 73 ...
##  $ potential          : int  71 71 66 65 65 76 76 75 75 75 ...
##  $ preferred_foot     : chr  "right" "right" "right" "right" ...
##  $ attacking_work_rate: chr  "medium" "medium" "medium" "medium" ...
##  $ defensive_work_rate: chr  "medium" "medium" "medium" "medium" ...
##  $ crossing           : int  49 49 49 48 48 80 80 79 79 79 ...
##  $ finishing          : int  44 44 44 43 43 53 53 52 51 51 ...
##  $ heading_accuracy   : int  71 71 71 70 70 58 58 57 57 57 ...
##  $ short_passing      : int  61 61 61 60 60 71 71 70 70 70 ...
##  $ volleys            : int  44 44 44 43 43 40 32 29 29 29 ...
##  $ dribbling          : int  51 51 51 50 50 73 73 71 71 71 ...
##  $ curve              : int  45 45 45 44 44 70 70 68 68 68 ...
##  $ free_kick_accuracy : int  39 39 39 38 38 69 69 69 69 69 ...
##  $ long_passing       : int  64 64 64 63 63 68 68 68 68 68 ...
##  $ ball_control       : int  49 49 49 48 48 71 71 70 70 70 ...
##  $ acceleration       : int  60 60 60 60 60 79 79 79 79 79 ...
##  $ sprint_speed       : int  64 64 64 64 64 78 78 78 78 78 ...
##  $ agility            : int  59 59 59 59 59 78 78 78 78 78 ...
##  $ reactions          : int  47 47 47 46 46 67 67 67 67 67 ...
##  $ balance            : int  65 65 65 65 65 90 90 90 90 90 ...
##  $ shot_power         : int  55 55 55 54 54 71 71 71 71 71 ...
##  $ jumping            : int  58 58 58 58 58 85 85 84 84 84 ...
##  $ stamina            : int  54 54 54 54 54 79 79 79 79 79 ...
##  $ strength           : int  76 76 76 76 76 56 56 56 56 56 ...
##  $ long_shots         : int  35 35 35 34 34 62 60 59 58 58 ...
##  $ aggression         : int  71 71 63 62 62 68 68 67 67 67 ...
##  $ interceptions      : int  70 70 41 40 40 67 67 66 66 66 ...
##  $ positioning        : int  45 45 45 44 44 60 60 58 58 58 ...
##  $ vision             : int  54 54 54 53 53 66 66 65 65 65 ...
##  $ penalties          : int  48 48 48 47 47 59 59 59 59 59 ...
##  $ marking            : int  65 65 65 62 62 76 76 76 76 76 ...
##  $ standing_tackle    : int  69 69 66 63 63 75 75 75 75 75 ...
##  $ sliding_tackle     : int  69 69 69 66 66 78 78 78 78 78 ...
##  $ gk_diving          : int  6 6 6 5 5 14 14 14 14 14 ...
##  $ gk_handling        : int  11 11 11 10 10 7 7 7 7 7 ...
##  $ gk_kicking         : int  10 10 10 9 9 9 9 9 9 9 ...
##  $ gk_positioning     : int  8 8 8 7 7 9 9 9 9 9 ...
##  $ gk_reflexes        : int  8 8 8 7 7 12 12 12 12 12 ...
```

```r
# Join player and player_stats by player_api_id column and rename it as id
dat<- player_stats %>%
  rename(player_stats_id = id) %>%
  left_join(player, by = "player_api_id")

# Check all the columns the newly created table!!
str(dat)
```

```
## Classes 'tbl_df', 'tbl' and 'data.frame':	183253 obs. of  48 variables:
##  $ player_stats_id     : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ player_fifa_api_id.x: int  218353 218353 218353 218353 218353 189615 189615 189615 189615 189615 ...
##  $ player_api_id       : int  505942 505942 505942 505942 505942 155782 155782 155782 155782 155782 ...
##  $ date_stat           : chr  "2016-02-18 00:00:00" "2015-11-19 00:00:00" "2015-09-21 00:00:00" "2015-03-20 00:00:00" ...
##  $ overall_rating      : int  67 67 62 61 61 74 74 73 73 73 ...
##  $ potential           : int  71 71 66 65 65 76 76 75 75 75 ...
##  $ preferred_foot      : chr  "right" "right" "right" "right" ...
##  $ attacking_work_rate : chr  "medium" "medium" "medium" "medium" ...
##  $ defensive_work_rate : chr  "medium" "medium" "medium" "medium" ...
##  $ crossing            : int  49 49 49 48 48 80 80 79 79 79 ...
##  $ finishing           : int  44 44 44 43 43 53 53 52 51 51 ...
##  $ heading_accuracy    : int  71 71 71 70 70 58 58 57 57 57 ...
##  $ short_passing       : int  61 61 61 60 60 71 71 70 70 70 ...
##  $ volleys             : int  44 44 44 43 43 40 32 29 29 29 ...
##  $ dribbling           : int  51 51 51 50 50 73 73 71 71 71 ...
##  $ curve               : int  45 45 45 44 44 70 70 68 68 68 ...
##  $ free_kick_accuracy  : int  39 39 39 38 38 69 69 69 69 69 ...
##  $ long_passing        : int  64 64 64 63 63 68 68 68 68 68 ...
##  $ ball_control        : int  49 49 49 48 48 71 71 70 70 70 ...
##  $ acceleration        : int  60 60 60 60 60 79 79 79 79 79 ...
##  $ sprint_speed        : int  64 64 64 64 64 78 78 78 78 78 ...
##  $ agility             : int  59 59 59 59 59 78 78 78 78 78 ...
##  $ reactions           : int  47 47 47 46 46 67 67 67 67 67 ...
##  $ balance             : int  65 65 65 65 65 90 90 90 90 90 ...
##  $ shot_power          : int  55 55 55 54 54 71 71 71 71 71 ...
##  $ jumping             : int  58 58 58 58 58 85 85 84 84 84 ...
##  $ stamina             : int  54 54 54 54 54 79 79 79 79 79 ...
##  $ strength            : int  76 76 76 76 76 56 56 56 56 56 ...
##  $ long_shots          : int  35 35 35 34 34 62 60 59 58 58 ...
##  $ aggression          : int  71 71 63 62 62 68 68 67 67 67 ...
##  $ interceptions       : int  70 70 41 40 40 67 67 66 66 66 ...
##  $ positioning         : int  45 45 45 44 44 60 60 58 58 58 ...
##  $ vision              : int  54 54 54 53 53 66 66 65 65 65 ...
##  $ penalties           : int  48 48 48 47 47 59 59 59 59 59 ...
##  $ marking             : int  65 65 65 62 62 76 76 76 76 76 ...
##  $ standing_tackle     : int  69 69 66 63 63 75 75 75 75 75 ...
##  $ sliding_tackle      : int  69 69 69 66 66 78 78 78 78 78 ...
##  $ gk_diving           : int  6 6 6 5 5 14 14 14 14 14 ...
##  $ gk_handling         : int  11 11 11 10 10 7 7 7 7 7 ...
##  $ gk_kicking          : int  10 10 10 9 9 9 9 9 9 9 ...
##  $ gk_positioning      : int  8 8 8 7 7 9 9 9 9 9 ...
##  $ gk_reflexes         : int  8 8 8 7 7 12 12 12 12 12 ...
##  $ id                  : int  1 1 1 1 1 2 2 2 2 2 ...
##  $ player_name         : chr  "Aaron Appindangoye" "Aaron Appindangoye" "Aaron Appindangoye" "Aaron Appindangoye" ...
##  $ player_fifa_api_id.y: int  218353 218353 218353 218353 218353 189615 189615 189615 189615 189615 ...
##  $ birthday            : chr  "1992-02-29 00:00:00" "1992-02-29 00:00:00" "1992-02-29 00:00:00" "1992-02-29 00:00:00" ...
##  $ height              : num  183 183 183 183 183 ...
##  $ weight              : int  187 187 187 187 187 146 146 146 146 146 ...
```

As you may have found, *date_stat* contains several dates. I will choose the latest data.

*date_stat* 필드에 여러 날짜가 있습니다. 저는 가장 최근 데이터를 쓰도록 하겠습니다.


```r
latest_dat<- dat %>%
  group_by(player_api_id)%>%
  top_n(n=1, wt=date_stat)%>%
  as.data.frame()
```

Now we can see it in data frame format, which is more readable!

읽기 쉽도록 위에서 data frame으로 타입을 변경한 걸 알수있습니다.

```r
head(latest_dat)
```

```
##   player_stats_id player_fifa_api_id.x player_api_id           date_stat
## 1               1               218353        505942 2016-02-18 00:00:00
## 2               6               189615        155782 2016-04-21 00:00:00
## 3              39               186170        162549 2016-01-07 00:00:00
## 4              65               140161         30572 2016-04-21 00:00:00
## 5              88                17725         23780 2015-12-24 00:00:00
## 6             113               158138         27316 2016-04-28 00:00:00
##   overall_rating potential preferred_foot attacking_work_rate
## 1             67        71          right              medium
## 2             74        76           left                high
## 3             65        67          right              medium
## 4             69        69          right              medium
## 5             70        70          right              medium
## 6             77        77           left              medium
##   defensive_work_rate crossing finishing heading_accuracy short_passing
## 1              medium       49        44               71            61
## 2              medium       80        53               58            71
## 3              medium       64        58               60            66
## 4              medium       57        21               68            65
## 5              medium       46        34               73            65
## 6              medium       77        72               64            79
##   volleys dribbling curve free_kick_accuracy long_passing ball_control
## 1      44        51    45                 39           64           49
## 2      40        73    70                 69           68           71
## 3      52        66    67                 63           62           67
## 4      48        56    38                 40           61           62
## 5      33        47    42                 25           58           58
## 6      77        78    77                 76           70           79
##   acceleration sprint_speed agility reactions balance shot_power jumping
## 1           60           64      59        47      65         55      58
## 2           79           78      78        67      90         71      85
## 3           77           74      85        51      92         61      65
## 4           33           34      62        57      41         59      71
## 5           33           31      41        68      52         48      67
## 6           75           69      76        80      72         76      65
##   stamina strength long_shots aggression interceptions positioning vision
## 1      54       76         35         71            70          45     54
## 2      79       56         62         68            67          60     66
## 3      66       71         57         60            55          64     68
## 4      49       90         29         70            62          26     54
## 5      60       75         26         66            68          29     41
## 6      75       65         76         59            55          77     76
##   penalties marking standing_tackle sliding_tackle gk_diving gk_handling
## 1        48      65              69             69         6          11
## 2        59      76              75             78        14           7
## 3        61      23              22             24        16          11
## 4        37      72              71             68        15          12
## 5        45      75              73             71         8           6
## 6        81      28              34             35        15          12
##   gk_kicking gk_positioning gk_reflexes id        player_name
## 1         10              8           8  1 Aaron Appindangoye
## 2          9              9          12  2    Aaron Cresswell
## 3         12              9          13  3        Aaron Doran
## 4         13             12          11  4      Aaron Galindo
## 5         16             12          11  5       Aaron Hughes
## 6          7             16          15  6         Aaron Hunt
##   player_fifa_api_id.y            birthday height weight
## 1               218353 1992-02-29 00:00:00 182.88    187
## 2               189615 1989-12-15 00:00:00 170.18    146
## 3               186170 1991-05-13 00:00:00 170.18    163
## 4               140161 1982-05-08 00:00:00 182.88    198
## 5                17725 1979-11-08 00:00:00 182.88    154
## 6               158138 1986-09-04 00:00:00 182.88    161
```

Let's look at key characteristics of the top 10 players.

탑 10 선수들의 특징을 살펴보록 합시당.

```r
#We're using DT library here
top10<- latest_dat %>%
  arrange(desc(overall_rating)) %>%
  head(n=10) %>%
  as.data.frame()

top10 %>%
  select(player_name,
         preferred_foot,
         height,
         weight,
         overall_rating) %>%
  datatable(., options=list(pageLength = 10))
```

```
## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.
```

```
## Warning in normalizePath(path.expand(path), winslash, mustWork): path[1]=".
## \webshot143c72e838ee.png": 지정된 파일을 찾을 수 없습니다
```

```
## Warning in file(con, "rb"): cannot open file 'C:\Users\SCSC\AppData\Local
## \Temp\Rtmpu0uGWq\file143c156a458e\webshot143c72e838ee.png': No such file or
## directory
```

```
## Error in file(con, "rb"): cannot open the connection
```

Unfortunately, the data does not contain information about each player's position. I guess we can differentiate goalies from field players by looking at the values of gk fields. Or we can cluster players, then use predictive analytics to guess the position for each player! For --laziness-- time sake, I will skip that part.

아쉽게도 각 선수의 포지션은 나와 있지 않습니다 ㅠㅠ gk필드의 값을 이용해 골키퍼는 구분할 수 있을지 몰라도.. 아니면 predictive analytics를 이용해 각 선수의 포지션을 예측할 수 있는 모델을 만들 수도 있을 것 같네요 ㅎㅎ 하지만 --귀찮은--시간이 없는 관계로 과감히 skip!!

## Interactive/Dynamic Plots
### Correlation matrix
Are there any correlations between the variables?
You can click on any data points to see a scatter plot.

왼쪽 자그마한 네모를 클릭하면 오른쪽에 두가지 변수의 상관관계를 나타내는 scatter plot을 볼 수 있습니다.


```r
library(qtlcharts)

iplotCorr(top10[,10:42], reorder = TRUE)
```

```
## Error in file(con, "rb"): cannot open the connection
```

### Radar chart
The radar chart below should look familiar to all those people who have played a sports game at least once in his/her life.
In order to create the chart, you must slightly transform the data.

피파나 다른 스포츠게임을 해본 사람이면 누구나 한번은 본 radar chart입니다. 밑에 차트를 생성하기 위해서는 데이터 포맷을 약간 바꿔줘야 합니다.


```r
library(radarchart)
library(tidyr)

new_df<- top10 %>%
  select(player_name, 10:42) %>%
  as.data.frame()

new_df<- gather(new_df, key=Label, value=Score, -player_name) %>%
  spread(key=player_name, value=Score)

chartJSRadar(scores = new_df, maxScale = 100, showToolTipLabel = TRUE)
```

```
## Error in file(con, "rb"): cannot open the connection
```

I wanted the plot to display player_name in descending order but could not find a way to do that... This is by far one of the coolest plots that R package supports!

마우커서를 올려놓았을 때, 선수이름을 순차적으로 나타내고 싶었는데 잘 안되네요.. 역시 R 패키지의 힘은 무한하네요 =)


