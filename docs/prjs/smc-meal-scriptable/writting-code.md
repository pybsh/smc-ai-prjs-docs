---
sidebar_position: 3
---
# 03. 코드 작성

## 1. 위젯 객체 생성
```jsx
let widget = new ListWidget();
widget.backgroundColor = new Color("#1f1e33");
```

- 위젯 객체를 생성해 `widget` 라는 변수에 저장합니다.
- `widget`의 배경 색을 `#1f1e33`으로 설정합니다.

## 2. 오늘 날짜 가져오기

```jsx
let locale = new Date().toLocaleString('en-US', { timeZone: 'Asia/Seoul' });
const date = new Date(); 
const year = date.getFullYear(); 
const month = (date.getMonth() + 1).toString().padStart(2, '0'); 
const day = date.getDate().toString().padStart(2, '0');
const today = `${year}${month}${day}`;
```

- 가져오는 시간을 서울 시간을 기준으로 설정합니다.
- 날짜를 하나씩 가져옵니다. (2월 2일은 02월 02일로 수정합니다)
- 년, 월, 일을 합쳐 하나의 문자열로 저장해 `today` 변수로 저장합니다.

## 3. 나이스의 API를 이용해 정보를 가져오기

```jsx
let sc_code = 'B10';
let school_code = '7010537';

let mealApiUrl = `https://open.neis.go.kr/hub/mealServiceDietInfo?Type=json&ATPT_OFCDC_SC_CODE=${sc_code}&SD_SCHUL_CODE=${school_code}&MLSV_FROM_YMD=${today}&MLSV_TO_YMD=${today}`;
let req = new Request(mealApiUrl);
req.method = "GET";
let res = await req.loadJSON();
```

- `sc_code`는 시도교육청코드를 담은 변수입니다. `B10`은 서울특별시교육청입니다.
- `school_code`는 행정표준코드를 담은 변수입니다. `7010537`는 세명컴퓨터고등학교의 행정표준코드입니다.
- `mealApiUrl`은 요청을 보낼 API의 엔드포인트입니다.
- `mealApiUrl`을 이용해 `GET` 요청을 보내고 그에 대한 답(Response)를 `res` 변수에 담습니다.

## 4. 가져온 정보를 파싱(Parsing)하기

```jsx
try {
    resStr = res.mealServiceDietInfo[1].row[0].DDISH_NM.replaceAll("<br/>","\n").replaceAll(/(\(([0-9]*\.*?)*\))/gi,'');
} catch {
    resStr = "-";
}
```

- 요청에 대한 답 `res`변수에서 급식 정보가 있는 부분을 가져와
    1. 줄내림(`\n`, `<br />`)을 제거합니다.
    2. 알레르기 정보가 담긴 부분(`5. 4. 12.`)을 제거합니다.

`resStr`에 저장을 시도합니다.

- 이를 실패할 경우, 급식이 없다고 판단하여 `resStr`에 `'-'`을 저장합니다.

## 5. 위젯 릴리즈

```jsx
let titleText = widget.addText(`[${month}/${day}] 급식🍔`);
titleText.centerAlignText();
titleText.font = Font.blackRoundedSystemFont(13);
titleText.textColor = new Color("#ffd700");

let widgetText = widget.addText(resStr);
widgetText.centerAlignText();
widgetText.font = Font.blackRoundedSystemFont(12);
widgetText.textColor = new Color("#ffffff");

if(!config.runsInWidget) {
    widget.presentSmall();
}

Script.setWidget(widget);
Script.complete();
```

- `titleText`는 위젯의 제목을 정해주는 변수입니다. 오늘의 날짜가 적혀있습니다.
- `widgetText`는 위젯의 내용을 정해주는 변수입니다. 오늘의 급식 정보가 적혀있습니다.
- 이들을 가운데 정렬(`centerAlignText();`)하고, 폰트를 설정합니다.
- `Script.setWidget(widget);` Scriptable 내장 함수를 이용해 위젯을 설정합니다.
- `Script.complete();` Scriptable 내장 함수를 이용해 완료함을 알린다.