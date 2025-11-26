<안전환경팀>
<html lang="ko">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>타워크레인 안전 자율 점검표 (표 + 대표 사진 고정 + 확대보기)</title>
  <style>
    :root{--bg:#f8fafc;--card:#ffffff;--muted:#64748b;--line:#cfd8e3;--ink:#0f172a;--blue:#0ea5e9;--blue-d:#0369a1;--orange:#f59e0b;--orange-d:#c2410c;--lbW:720px;--lbH:540px}
    html,body{height:100%}
    body{margin:0;background:var(--bg);color:var(--ink);font-family:system-ui,Segoe UI,Roboto,Apple SD Gothic Neo,Malgun Gothic,sans-serif}
    .container{max-width:860px;margin:0 auto;padding:12px}
    .head{display:flex;justify-content:space-between;align-items:center;gap:8px;margin-bottom:10px}
    .title{font-size:22px;font-weight:800;margin:0}
    .btns{display:flex;gap:8px;flex-wrap:wrap}
    .btn{padding:8px 10px;border-radius:10px;border:1px solid #d1d5db;background:#fff;cursor:pointer;font-size:14px}
    .card{background:var(--card);border:1px solid var(--line);border-radius:14px;box-shadow:0 4px 14px rgba(0,0,0,.06);padding:12px;margin-bottom:10px}
    .grid2{display:grid;grid-template-columns:1fr 1fr;gap:10px}
    .row{display:grid;grid-template-columns:100px 1fr;gap:8px;align-items:center}
    textarea,input[type="text"],input[type="datetime-local"]{padding:10px;border:1px solid #d1d5db;border-radius:10px;width:100%;box-sizing:border-box;font-size:16px;background:#fff}
    .note{font-size:12px;color:var(--muted)}

    /* ===== 표 ===== */
    .check-table{width:100%;border-collapse:collapse;background:#fff;border-radius:12px;overflow:hidden}
    .check-table th,.check-table td{border:1px solid var(--line);padding:8px;vertical-align:middle}
    .check-table th{background:#eef3f8;font-weight:800;text-align:center}
    .col-no{width:48px;text-align:center}
    .col-item{width:240px}
    .col-opt{width:70px;text-align:center}
    .itemcell{display:grid;grid-template-columns:96px 1fr;gap:8px;align-items:center}
    .thumb{width:96px;height:72px;border:1px solid var(--line);border-radius:8px;object-fit:contain;background:#fff;cursor:zoom-in;touch-action:manipulation;}

    /* 대표사진 */
    .hero-box{display:flex;align-items:center;justify-content:center;height:260px;border:1px solid var(--line);border-radius:12px;background:#fafafa}
    .hero-img{max-width:100%;max-height:100%;object-fit:contain;border-radius:10px;cursor:zoom-in}

    /* 라이트박스 */
    .lightbox{position:fixed;inset:0;display:none;align-items:center;justify-content:center;background:rgba(0,0,0,.7);z-index:10000}
    .lightbox.open{display:flex}
    .lb-img{max-width:92vw;max-height:86vh;border-radius:12px;background:#fff;box-shadow:0 10px 30px rgba(0,0,0,.35)}
    .lb-close{position:absolute;top:14px;right:16px;border:1px solid #e5e7eb;background:#fff;border-radius:10px;padding:6px 10px;cursor:pointer}

  </style>
</head>

<body>
  <div class="container">
    <header class="head">
      <h1 class="title">타워크레인 안전 자율 점검표</h1>
      <div class="btns">
        <button id="pngBtn" class="btn">🖼 이미지</button>
        <button id="resetBtn" class="btn">↺ 초기화</button>
      </div>
    </header>

    <div id="capture">
      <!-- 기본 정보 -->
      <section class="card">
        <h2 style="font-size:18px;font-weight:700;margin:0 0 8px">기본 정보</h2>
        <div class="grid2">
          <div class="row"><label>점검 일시</label><input id="date" type="datetime-local" /></div>
          <div class="row"><label>소속</label><input id="org" type="text" placeholder="예) 생산1팀" /></div>
          <div class="row"><label id="siteLabel">점검 장소</label><input id="site" type="text" placeholder="예) ○○현장" /></div>
          <div class="row"><label>점검자</label><input id="inspector" type="text" placeholder="성명" /></div>
          <div class="row"><label>장비 구분</label><input id="equip" type="text" value="타워크레인" /></div>
          <div class="row"><label>장비 ID/번호</label><input id="equipId" type="text" placeholder="예) TC-01" /></div>
        </div>
      </section>

      <!-- 대표 사진 -->
      <section class="card">
        <div class="hero-box">
          <img id="heroImg" class="hero-img" src="타워크레인.png" alt="대표 이미지" />
        </div>
      </section>

      <!-- 체크리스트 -->
      <section class="card table-wrap">
        <table class="check-table">
          <thead>
            <tr>
              <th>No</th>
              <th>점검항목</th>
              <th>방법</th>
              <th>양호</th>
              <th>불량</th>
            </tr>
          </thead>
          <tbody id="tblBody"></tbody>
        </table>

        <div class="grid2" style="margin-top:12px">
          <div>
            <div class="note">특이사항 / 비고</div>
            <textarea rows="5" id="remarks"></textarea>
          </div>
          <div>
            <div class="note">조치 내용</div>
            <textarea rows="5" id="action"></textarea>
          </div>
        </div>
      </section>
    </div>
  </div>

  <!-- 라이트박스 -->
  <div id="lightbox" class="lightbox">
    <img id="lbImg" class="lb-img" />
    <button id="lbClose" class="lb-close">닫기 ✕</button>
  </div>

  <script>
    const $ = (id)=>document.getElementById(id);

    /* 대표 이미지 */
    const HERO_SRC = "타워크레인.png";

    /* 체크리스트 */
    const checklistTemplate = [
      { key:'foundation_anchor', label:'기초 / 앵커볼트', method:'기초 균열, 침하, 앵커볼트 풀림 여부 확인', refImg:'기초_앵커볼트.png' },
      { key:'mast_section', label:'마스트 / 접속핀', method:'마스트 변형·균열 및 접속핀 빠짐 여부 확인', refImg:'마스트.png' },
      { key:'slewing_ring', label:'선회베어링', method:'누유·볼트풀림·파손 여부 확인', refImg:'선회베어링.png' },
      { key:'jib', label:'지브 / 카운터지브', method:'지브 처짐·균열·볼트풀림 여부 확인', refImg:'지브.png' },
      { key:'wire', label:'와이어로프', method:'가닥 끊어짐·마모·부식 여부 확인', refImg:'와이어.png' },
      { key:'hook', label:'훅 / 훅블록', method:'세이프티래치 정상작동 여부 확인', refImg:'훅.png' },
      { key:'safety_device', label:'안전장치', method:'과부하방지장치·권과방지장치 작동 확인', refImg:'안전장치.png' },
      { key:'ladder', label:'사다리 / 작업발판', method:'사다리 고정·손상 여부 확인', refImg:'사다리.png' },
      { key:'panel', label:'전기·제어반', method:'차단기, 케이블 손상 여부 확인', refImg:'전기.png' },
      { key:'cab', label:'운전실·조종장치', method:'조종장치 파손·경고등 여부 확인', refImg:'운전실.png' },
      { key:'area', label:'작업반경 / 주변구역', method:'장애물·출입통제 상태 확인', refImg:'작업구역.png' }
    ];

    const state = {
      checks:Object.fromEntries(checklistTemplate.map(i=>[i.key,''])),
      remarks:'',
      action:''
    };

    function renderTable(){
      const body=$('tblBody');
      body.innerHTML='';

      checklistTemplate.forEach((row,i)=>{
        const tr=document.createElement('tr');
        tr.innerHTML = `
          <td>${i+1}</td>
          <td><div class="itemcell">
                <img class="thumb" src="${row.refImg}" data-full="${row.refImg}">
                <div>${row.label}</div>
              </div></td>
          <td>${row.method}</td>
          <td><button class="opt-btn ok" data-key="${row.key}" data-val="양호">양호</button></td>
          <td><button class="opt-btn bad" data-key="${row.key}" data-val="불량">불량</button></td>
        `;
        body.appendChild(tr);
      });

      body.addEventListener('click',(e)=>{
        const b=e.target.closest('button');
        if(!b) return;
        state.checks[b.dataset.key] = b.dataset.val;
        document.querySelectorAll(`[data-key="${b.dataset.key}"]`).forEach(el=>{
          el.classList.remove('selected');
        });
        b.classList.add('selected');
      });
    }

    renderTable();
  </script>
</body>
</html>
