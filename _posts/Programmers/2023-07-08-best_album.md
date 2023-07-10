---
layout: single
title:  "프로그래머스 - 베스트앨범 (Lv.3)"
excerpt: "해시"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [베스트앨범](https://school.programmers.co.kr/learn/courses/30/lessons/42579)

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

typedef struct _song{
    string genre;
    int plays;
    int number;
} Song;

typedef struct _cate{
    int ctg_idx;
    int total_plays;
} Cate;

// 장르를 기준으로 내림차순 정렬
bool cmp_genre(Song s1, Song s2){
    return s1.genre > s2.genre;
}

// 재생 횟수 기준으로 내림차순 정렬
bool cmp_plays(Song s1, Song s2){
    return s1.plays > s2.plays;    
}

// Total plays 기준 내림차순 정렬
bool cmp_cat_plays(Cate c1, Cate c2){
    return c1.total_plays > c2.total_plays;
}

int get_genre_idx(vector<string>& categories, string genre){
    for(int i=0; i<categories.size(); i++){
        if(genre == categories[i])
            return i;
    }
    cout << "Find Genre idx Error\n";
    return -1;
}

vector<int> solution(vector<string> genres, vector<int> plays) {
    vector<int> answer;
    
    // update data
    vector<Song> songs;
    Song nsong;
    int len = genres.size();
    for(int i=0; i<len; i++){
        nsong.genre = genres[i];
        nsong.plays = plays[i];
        nsong.number = i;
        songs.push_back(nsong);
    }
    sort(songs.begin(), songs.end(), cmp_genre); // 장르로 내림차순 정렬
    
    // 장르를 인덱스로 표현할 수 있도록 한다.
    vector<string> categories;
    string cgenre = songs[0].genre;
    categories.push_back(cgenre);
    for(int i=1; i<len; i++){
        if(cgenre != songs[i].genre){
            cgenre = songs[i].genre;
            categories.push_back(cgenre);
        }
    }
    sort(songs.begin(), songs.end(), cmp_plays); // 재생 횟수로 내림차순 정렬
    int ctg_len = categories.size();
    
    // board[category][0]: category에 해당하는 장르에서 재생 횟수가 가장 많은 노래 번호
    // board[category][1]: category에 해당하는 장르에서 재생 횟수가 두번째로 많은 노래 번호
    vector<int> board[ctg_len]; 
    vector<int> total_plays(ctg_len, 0); // total_plays[category]: category에 해당하는 장르의 총 재생 횟수
    for(int i=0; i<len; i++){
        int ctg_idx = get_genre_idx(categories, songs[i].genre);
        total_plays[ctg_idx] += songs[i].plays;
        board[ctg_idx].push_back(songs[i].number);
    }
    
    vector<Cate> ctg_idx_arr;
    Cate tmp;
    for(int i=0; i<ctg_len; i++){
        tmp.ctg_idx = i;
        tmp.total_plays = total_plays[i];
        ctg_idx_arr.push_back(tmp);
    }
    sort(ctg_idx_arr.begin(), ctg_idx_arr.end(), cmp_cat_plays);
    
    for(int i=0; i<ctg_len; i++){
        int ctg_idx = ctg_idx_arr[i].ctg_idx;
        if(board[ctg_idx].size()==1){
            answer.push_back(board[ctg_idx][0]);
            continue;
        }
        int song1 = board[ctg_idx][0];
        int song2 = board[ctg_idx][1];
        int song1_plays, song2_plays;
        
        for(int i=0; i<len; i++){
            if(song1 == songs[i].number){
                song1_plays = songs[i].plays;
            }
            if(song2 == songs[i].number){
                song2_plays = songs[i].plays;
            }
        }
        
        if(song1_plays == song2_plays){
            if(song1 < song2) {
                answer.push_back(song1);
                answer.push_back(song2);
            }
            else{
                answer.push_back(song2);
                answer.push_back(song1);
            }
        }
        else{
            answer.push_back(song1);
            answer.push_back(song2);
        }
    }
    
    return answer;
}

```
- **Song** 구조체를 만들어서 장르, 재생횟수, 고유 번호를 담을 수 있도록 한다.
- 문자열로 주어진 장르를 인덱스로 접근할 수 있도록 인덱스화 한다.
    - 장르를 기준으로 내림차순 정렬을 하면 장르별 인덱스를 할당하기 편하다.
    - categories[0]: 0번에 해당하는 장르의 이름이 문자열로 담겨 있다.
- 이차원 배열을 선언한 후 장르별로 재생 횟수가 많은 순서대로 고유 번호를 담는다.
    - songs 배열을 재생횟수가 많은 순서대로 정렬한다.
    - songs 를 탐색하면서 각 노래의 장르 인덱스(ctg_idx)를 찾고, board[ctg_idx] 배열에 노래의 고유 번호를 담는다.
        - board[ctg_idx][0]: ctg_idx에 해당하는 장르에서 재생횟수가 가장 많은 노래의 고유번호
    - 장르별로 재생횟수의 총합을 계산하는 배열을 둔다.
        - total_plays[ctg_idx]: ctg_idx에 해당하는 장르의 전체 재생 횟수를 담는다.
- 전체 재생 횟수를 기준으로 장르 인덱스를 내림차순 정렬한다.
- 졍렬된 장르 인덱스 순서대로 board 에 접근하여 노래의 고유 번호를 answer에 담는다.


