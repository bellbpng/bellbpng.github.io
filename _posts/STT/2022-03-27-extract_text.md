---
layout: single
title:  "Google Cloud STT 결과 텍스트파일로 추출하기 "
excerpt: "Google Cloud Platform, Speech-to-Text"

categories:
  - SpeechToText
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

이번 포스팅에서는 이전 포스팅에서 구글 STT 소프트웨어를 통해 얻은 결과파일(.json)을 텍스트(.txt) 파일로 추출하는 방법에 대해 다룰 것이다.

이것 역시 이전 글과 마찬가지로 상당부분 같은 기술블로그를 참고해서 작성하였다.

[블로그 링크] [google cloud platform을 이용하여 speech-to-text로 음성을 텍스트로 변환해보자](https://ehdrh789.tistory.com/29)


## Python을 이용하여 필요한 내용 추출

### .json 파일 구조

- STT 결과로 얻은 .json 파일 구조는 아래와 같다.

```json
"response": {
    "@type": "type.googleapis.com/google.cloud.speech.v1.LongRunningRecognizeResponse",
    "results": [
      {
        "alternatives": [
          {
            "confidence": 0.47926497,
            "transcript": "이건희 핸드폰"
          }
        ],
        "channelTag": 1,
        "languageCode": "ko-kr",
        "resultEndTime": "7.710s"
      },
      {
        "alternatives": [
          {
            "confidence": 0.6483066,
            "transcript": "이건 여기 핸드폰"
          }
        ],
        "channelTag": 2,
        "languageCode": "ko-kr",
        "resultEndTime": "14.130s"
      },
      {
        "alternatives": [
          {
            "confidence": 0.92365956,
            "transcript": " 아"
          }
        ],
        "channelTag": 2,
        "languageCode": "ko-kr",
        "resultEndTime": "35.480s"
      },
      {
        "alternatives": [
          {
            "confidence": 0.92365956,
            "transcript": " 아"
          }
        ],
        "channelTag": 1,
        "languageCode": "ko-kr",
        "resultEndTime": "35.540s"
      },
      {
        "alternatives": [
          {
            "confidence": 0.88758534,
            "transcript": " 너 오른쪽 왼쪽 알아"
          }
        ],
        "channelTag": 2,
        "languageCode": "ko-kr",
        "resultEndTime": "43.030s"
      },
      {
        "alternatives": [
          {
            "confidence": 0.87635326,
            "transcript": " 너 오른쪽 왼쪽 알아 알아요 왼쪽 오른쪽 오 잘하네 야 그럼 너 혼자 가게 가서 초콜릿 과자 있단 거 산 적 있어"
          }
        ],
        "channelTag": 1,
        "languageCode": "ko-kr",
        "resultEndTime": "53.630s"
      },
      {
        "alternatives": [
          {
            "confidence": 0.83036506,
            "transcript": " 알아요 왼쪽 오른쪽 잘하네 야 그럼 너 혼자 가게 가서 초콜릿 과자 있단 거 산 적 있어"
          }
        ],
        "channelTag": 2,
        "languageCode": "ko-kr",
        "resultEndTime": "53.830s"
      },
```

- 가지고 있는 데이터가 stereo 타입이었기 때문에 2개의 채널에 대한 결과가 나오는 것을 확인할 수 있다. 따라서 두 채널 중에 정확도(confidence)가 상대적으로 더 높은 1번 채널의 결과만을 추출하기로 결정했다.
- STT가 진행된 구간을 함께 파악하고 싶어서 "resultEndTime"의 결과를 이용해서 각 stt 결과를 구간과 함께 작성할 수 있도록 했다.
- 한 줄로 길게 입력되는 경우 가독성이 떨어지는 문제가 있어서 단어 10개 정도마다 개행문자를 추가해서 출력하여 이를 보완하기로 했다.

### python 코드

- 아래는 .txt 파일로 추출하는 코드이다. 상당부분은 위에서 언급한 블로그 코드를 참고했다.

```python
import argparse
import io
import json


def parsing(filepath):

    fileToWrite = open(filepath[0:-4]+"txt", "w")

    with open(filepath, 'rt', encoding="UTF-8") as json_file:
        data = json.load(json_file)
        i = 1
        idx = 1
        start_time = "0.000s"
        for alts in data['response']['results']:
            if i%2==0: # 1번 채널만 선택
                i += 1
                continue
            fileToWrite.write(str(idx) + " : ")
            fileToWrite.write(start_time + " ~ ")
            end_time = str(alts['resultEndTime'])
            fileToWrite.write(end_time)
            fileToWrite.write("\n")
            start_time = end_time
            # fileToWrite.write(str(alts['alternatives'][0]['transcript']))
            txt = str(alts['alternatives'][0]['transcript']).split()
            cnt = 0
            for token in txt:
                if cnt == 10:
                    fileToWrite.write("\n")
                    cnt = 0
                fileToWrite.write(token)
                fileToWrite.write(" ")
                cnt += 1
            fileToWrite.write("\n\n\n")
            i += 1
            idx += 1

    fileToWrite.close()


if __name__ == '__main__':
    parser = argparse.ArgumentParser(
        description=__doc__,
        formatter_class=argparse.RawDescriptionHelpFormatter)
    parser.add_argument(
        'path', help='File or GDX path for audio file to be recognized')
    args = parser.parse_args()
    parsing(args.path)

```

