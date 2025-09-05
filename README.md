<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QA 自動回覆</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      background-color: #1a1616;
      display: flex;
      justify-content: center;
      align-items: center;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    .qa-container {
      background-color: #2c2c2c;
      border: 6px solid #a67c52;
      border-radius: 20px;
      padding: 30px 25px;
      width: 90%;
      max-width: 1000px;
      box-sizing: border-box;
      overflow-y: auto;
      max-height: 95vh;
    }

    .qa-item {
      margin-bottom: 20px;
    }

    .question {
      cursor: pointer;
      font-size: 1.5rem;
      color: #FFC107;
      margin-bottom: 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background-color: #444;
      padding: 12px 16px;
      border-radius: 10px;
      transition: background 0.3s;
    }

    .question:hover {
      background-color: #555;
    }

    .arrow {
      font-size: 1.2rem;
      color: #FFC107;
    }

    .answer {
      max-height: 0;
      overflow: hidden;
      transition: max-height 0.4s ease, padding 0.3s ease;
      font-size: 1.1rem;
      color: #ffffff;
      padding-left: 20px;
      line-height: 1.6;
      background-color: #3a3a3a;
      border-radius: 8px;
      padding: 0 16px;
    }

    .answer.open {
      max-height: 800px;
      padding: 15px 16px;
    }

    @media (max-width: 600px) {
      .qa-container {
        padding: 20px 15px;
      }
      .question {
        font-size: 1.2rem;
      }
      .answer {
        font-size: 1rem;
      }
    }
  </style>
</head>
<body>

  <div class="qa-container" id="qa">
    <!-- QA 內容將由 JS 注入 -->
  </div>

  <script>
    const qaData = [
      {
    q: "駐車場や電気自動車の充電設備はありますか？",
    a: `当ホテルでは、地下1階に高さ制限2.2mの無料平面駐車場を提供しております。ご到着後、直接地下に駐車し、エレベーターで1階フロントへお越しください。駐車スペースには限りがあるため、予約は承っておりません。<br><br>
        ⭐ 旧正月や週末など混雑時にはお待たせする場合がございます。あらかじめご了承ください。<br>
        ⭐ 館内にはEV充電設備はございません。`
  },
  {
    q: "朝食内容 ・ 提供時間は?",
    a: `▪ ビュッフェ形式で中華・洋食・和食をご用意しております。<br>
        ▪ 時間：6:30〜10:00、部屋番号と人数を確認後ご利用可能です。<br><br>
       ⭐ 繁忙期（年末年始など）は利用時間を1時間に制限する場合があります。`
  },
  {
    q: "ベビーベッドやベッドガードの貸し出しはありますか？",
    a: `無料でベビーベッドやベッドガードの貸し出しを行っております。<br>
        ▪ 安全面から、ベビーベッドは1歳未満のお子様に限ります。<br>
        ▪ 1歳以上のお子様にはベッドガードをおすすめします（チェックイン前に設置が必要）。<br>
        ▪ 数に限りがありますので、ご予約時にお申し付けください。<br>
        ▪ スタンダードダブルルームにはベビーベッドを設置できません。`
  },
  {
    q: "ベビーバスや哺乳瓶の消毒器はありますか？",
    a: `▪ ベビーバス（小さな椅子付き）と哺乳瓶消毒器を無料で貸し出しております。<br><br>
      ⭐ 数に限りがあるため、事前のご予約をおすすめします。`
  },
  {
    q: "バスルームに浴槽はありますか？",
    a: `▪ スタンダードダブルルーム：小型座浴槽のみ<br>
        ▪ デラックスルーム以上：標準サイズの一人用浴槽あり`
  },
  {
    q: "子どもの宿泊料金は？",
    a: `▪ 1歳未満はベビーベッド利用可（要予約）<br>
        ▪ 109cm未満：無料<br>
        ▪ 110～139cm：NT$220<br>
        ▪ 140cm以上：大人料金、エキストラベッド推奨<br>
        ▪ エキストラベッド朝食付き：NT$880（旧正月はNT$1200）<br>
           朝食なし：NT$550（旧正月はNT$1000）`
  },
  {
    q: "大人の追加料金は？",
    a: `▪ 朝食付きエキストラベッド：NT$880（旧正月はNT$1200）<br>
        ベッドなし追加宿泊：NT$500（旧正月はNT$800）<br><br>
        ▪ 朝食なしエキストラベッド：NT$550（旧正月はNT$1000）<br>
        ベッドなし追加宿泊：NT$400（旧正月はNT$500）<br>
        ⭐ スタンダードダブルルームではエキストラベッドの提供はできません。`
  },

       {
    q: "宿泊に年齢制限はありますか？",
    a: `▪ 宿泊のチェックインは⭐20歳以上の方に限ります。`
  },
  {
    q: "平日と休日の定義は？",
    a: `▪ 平日：日曜〜金曜<br>▪ 休日：土曜および祝日`
  },
  {
    q: "公共交通機関や送迎サービスについて教えてください。",
    a: `▪ 台鉄・新竹駅から徒歩約8分（東門国小裏門経由）<br>
        ▪ 高鉄・新竹駅 → 徒歩2分で空中通路を通り六家駅 → 台鉄で新竹駅へ → 徒歩8分で当ホテルへ<br>
        ▪ 無料送迎サービスは行っておりません。`
  },
  {
    q: "チェックイン・チェックアウト時間、荷物預かりについて",
    a: `▪ チェックイン：15:00以降、チェックアウト：翌日11:00まで。<br>
       ⭐ 部屋タイプ指定なしのお得プラン「19:00以降チェックイン限定」。<br>
        ▪ チェックイン前およびチェックアウト後の荷物預かり可能です。`
  }

    ];

    const container = document.getElementById('qa');

    qaData.forEach((item, index) => {
      const qaItem = document.createElement('div');
      qaItem.className = 'qa-item';

      const question = document.createElement('div');
      question.className = 'question';
      question.innerHTML = `・${item.q} <span class="arrow">▼</span>`;
      question.onclick = () => toggleAnswer(index);

      const answer = document.createElement('div');
      answer.className = 'answer';
      answer.innerHTML = item.a;

      qaItem.appendChild(question);
      qaItem.appendChild(answer);
      container.appendChild(qaItem);
    });

    function toggleAnswer(index) {
      const answers = document.querySelectorAll('.answer');
      const arrows = document.querySelectorAll('.arrow');
      const answer = answers[index];
      const arrow = arrows[index];
      const isOpen = answer.classList.contains('open');

      answer.classList.toggle('open');
      arrow.textContent = isOpen ? '▼' : '▲';
    }

    // 預設展開第一個問題
    window.onload = () => toggleAnswer(0);
  </script>
</body>
</html>
