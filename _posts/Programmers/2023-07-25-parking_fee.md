---
layout: single
title:  "프로그래머스 - 주차 요금 계산 (Lv.2)"
excerpt: "2022 KAKAO BLIND RECRUITMENT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [주차 요금 계산](https://school.programmers.co.kr/learn/courses/30/lessons/92341)

## 문제 접근
- 문자열 파싱은 stringstream 을 활용한다. 
    - **>>** 연산자는 공백문자를 만날 떄까지 버퍼의 문자를 읽어들이고, 공백문자는 버퍼에 남겨둔다.
    - get() 함수는 공백문자를 포함한 문자 하나를 버퍼에서 읽어온다.
- 들어온 차량에 대한 데이터를 담는 배열(**inArr**)과 나가는 차량에 대한 데이터를 담는 배열(**outArr**)을 선언하여, 입/출차 기록에 따라 차량 번호와 시간을 분 단위로 계산하여 담는다.
- 차량의 누적시간을 담는 자료구조로 **map** 자료구조를 활용한다.
    - **inArr** 배열과 **outArr** 배열 모두 중복된 차량 번호가 있을 수 있다. **inArr** 과 **outArr** 을 앞에서부터 탐색하며 비교하면서 방문표시를 남기며 누적 시간을 계산한다.
    - 출차 기록이 없는 차량은 **inVisited** 배열에서 false 로 표시되어 있으므로, 해당 차량은 23시 59분을 출차 시간으로 간주하여 계산을 추가한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <sstream>
#include <iostream>
#include <map>

using namespace std;

map<int, int> updateTotalTime(vector<pair<int, int>>& inArr, vector<pair<int, int>>& outArr){
    map<int, int> ret;
    vector<bool> outVisited(outArr.size(), false);
    vector<bool> inVisited(inArr.size(), false);
    
    int carNum;
    for(int i=0; i<inArr.size(); i++){
        carNum = inArr[i].first;
        for(int j=0; j<outArr.size(); j++){
            if(carNum==outArr[j].first && outVisited[j]==false){
                inVisited[i] = true;
                outVisited[j]=true;
                if(ret.find(carNum)==ret.end())
                    ret[carNum] = outArr[j].second - inArr[i].second;
                else
                    ret[carNum] += (outArr[j].second - inArr[i].second);
                break;
            }
        }
    }
    
    // 나가지 못한 차량 시간 계산
    int endTime = 23*60 + 59;
    for(int i=0; i<inVisited.size(); i++){
        if(inVisited[i]==false){
            carNum = inArr[i].first;
            // cout << "car num: " << carNum << ", " << "cur time: " << inArr[i].second << endl;
            if(ret.find(carNum)==ret.end())
                ret[carNum] = endTime - inArr[i].second;
            else
                ret[carNum] += (endTime - inArr[i].second);
        }
    }
    
    return ret;
}

vector<int> solution(vector<int> fees, vector<string> records) {
    vector<int> answer;
    vector<pair<int, int>> inArr, outArr;
    // parsing
    string state, time, hour, min, scarNum;
    int carNum;
    stringstream ss;
    for(int i=0; i<records.size(); i++){
        ss.clear();
        ss.str(records[i]);
        ss >> time >> scarNum >> state;
        hour.push_back(time[0]), hour.push_back(time[1]);
        min.push_back(time[3]), min.push_back(time[4]);
        int tMin = stoi(hour)*60 + stoi(min);
        hour.clear(); min.clear();
        carNum = stoi(scarNum);
        if(state=="IN"){
            inArr.push_back(make_pair(carNum, tMin));
        }
        else if(state=="OUT"){
            outArr.push_back(make_pair(carNum, tMin));
        }
    }
    
    map<int, int> carTime = updateTotalTime(inArr, outArr);
    int parkingTime, totalFee;
    bool alpha;
    for(auto ele:carTime){
        // cout << "car num: " << ele.first << ", " << ele.second << endl;
        parkingTime = ele.second;
        if(parkingTime < fees[0]) // 기본 시간보다 작은 경우 기본 요금 청구
            answer.push_back(fees[1]);
        else{
            alpha = (parkingTime - fees[0]) % fees[2];
            totalFee = fees[1] + ((parkingTime - fees[0])/fees[2] + alpha) * fees[3];
            answer.push_back(totalFee);
        }
    }

    return answer;
}
``` 
- stringstream 은 str() 메소드를 활용하여 버퍼를 채울 수 있다.
    - **ss.str(records[i]);**


### 구현2
```c++
#include <string>
#include <vector>
#include <map>
#include <algorithm>
#include <iostream>
#include <sstream>

using namespace std;

map<string, int> table;

int getMinutes(string time){
    int ret = 0;
    stringstream stm(time);
    string hour, minute;
    getline(stm, hour, ':');
    stm >> minute;
    
    ret = stoi(hour) * 60 + stoi(minute);
    return ret;
}

void updateTable(vector<string>& records)
{
    map<string, int> enterInfo; // 차량이 들어온 시간 정보
    for(int i=0; i<records.size(); i++){
        stringstream stm(records[i]);
        string time, carNumber, status;
        stm >> time >> carNumber >> status;
        if (status == "IN") {
            enterInfo[carNumber] = getMinutes(time);
        }
        else if (status == "OUT") {
            table[carNumber] += (getMinutes(time) - enterInfo[carNumber]);
            enterInfo[carNumber] = -1; // 주차장을 빠져 나간 경우
        }
    }
    
    // 출차된 내역이 없는 경우
    for (auto ele : enterInfo) {
        if (ele.second != -1) {
            table[ele.first] += (getMinutes("23:59") - ele.second);
        }
    }
    
}

bool cmp(pair<string, int> p1, pair<string, int> p2)
{
    return p1.first < p2.first;
}

int getExtraFee(int exceedTime, int unitTime, int unitFee)
{
    if (exceedTime % unitTime != 0) return (exceedTime / unitTime + 1) * unitFee;
    return (exceedTime / unitTime) * unitFee;
}

void calculateParkingFee(vector<int>& fees, vector<int>& answer)
{
    map<string, int> feeInfo;
    int defaultTime = fees[0];
    int defaultFee = fees[1];
    int unitTime = fees[2];
    int unitFee = fees[3];
    
    for (auto ele : table) {
        if (ele.second <= defaultTime) feeInfo[ele.first] = defaultFee;
        else if (ele.second > defaultTime) {
            feeInfo[ele.first] = defaultFee + getExtraFee(ele.second - defaultTime, unitTime, unitFee);
        }
    }
    vector<pair<string, int>> tmp(feeInfo.begin(), feeInfo.end());
    sort(tmp.begin(), tmp.end(), cmp);
    
    for(auto ele : tmp){
        answer.push_back(ele.second);
    }
}

vector<int> solution(vector<int> fees, vector<string> records) {
    vector<int> answer;
    updateTable(records);
    calculateParkingFee(fees, answer);
    return answer;
}

```