<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>퀴즈 관리자 v4.3.1 (GPT 연동·중복 방지·OX 보기=O/X만)</title>
<style>
  :root{
    --primary:#4f46e5; --primary-600:#4338ca; --muted:#6b7280;
    --bg:#f9fafb; --card:#ffffff; --border:#e5e7eb; --danger:#ef4444; --ok:#16a34a; --ok-bg:#ecfdf5;
  }
  *{box-sizing:border-box}
  body{margin:0;font-family:'Segoe UI',system-ui,-apple-system,Roboto,'Noto Sans KR',sans-serif;background:var(--bg);color:#111827}
  .container{max-width:960px;margin:0 auto;padding:16px}
  .card{background:var(--card);border:1px solid var(--border);border-radius:12px;box-shadow:0 2px 8px rgba(0,0,0,.05);padding:16px}
  header.app{position:sticky;top:0;z-index:10;background:linear-gradient(90deg,#4f46e5,#6366f1);color:#fff;padding:12px 16px;box-shadow:0 2px 8px rgba(0,0,0,.1);display:flex;gap:10px;justify-content:space-between;align-items:center}
  header.app h1{font-size:16px;margin:0}
  .btn{border:0;border-radius:10px;padding:10px 14px;font-weight:700;cursor:pointer;transition:.2s}
  .btn.primary{background:var(--primary);color:#fff}
  .btn.primary:hover{background:var(--primary-600)}
  .btn.secondary{background:#e5e7eb}
  .btn.danger{background:var(--danger);color:#fff}
  .btn.ghost{background:transparent;border:1px solid #ffffff33;color:#fff}
  .hidden{display:none !important}
  .toolbar{display:flex;gap:10px;justify-content:space-between;align-items:center;flex-wrap:wrap;margin-bottom:12px}
  .title{font-size:18px;font-weight:800;margin:0}
  table{width:100%;border-collapse:collapse;background:#fff;border:1px solid var(--border);border-radius:10px;overflow:hidden}
  th,td{padding:12px;border-bottom:1px solid var(--border);text-align:left}
  th{background:#f3f4f6;font-size:13px}
  tr:hover{background:#fafafa}
  a{color:#4f46e5;text-decoration:none}
  a:hover{text-decoration:underline}
  input,select,textarea{width:100%;padding:10px;border:1px solid var(--border);border-radius:10px;font-size:14px;background:#fff}
  textarea{resize:vertical;min-height:140px}
  .muted{color:var(--muted);font-size:12px}
  .badge{display:inline-block;padding:4px 8px;border-radius:999px;font-size:12px}
  .badge.on{background:#dcfce7;color:#166534}
  .badge.off{background:#fee2e2;color:#991b1b}
  .top-actions{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
  /* Toggle */
  .toggle-group{display:flex;align-items:center;gap:10px;padding:8px 10px;border:1px solid var(--border);border-radius:12px;background:#f8fafc}
  .switch{position:relative;width:46px;height:26px;background:#d1d5db;border-radius:999px;transition:.2s}
  .switch::after{content:"";position:absolute;top:3px;left:3px;width:20px;height:20px;background:#fff;border-radius:999px;transition:.2s;box-shadow:0 1px 2px rgba(0,0,0,.15)}
  .switch.on{background:#22c55e}
  .switch.on::after{transform:translateX(20px)}
  .switch-label{font-weight:700;font-size:13px}
  .switch-hint{font-size:12px;color:#6b7280}
  .form-grid{display:grid;grid-template-columns:1fr 1fr;gap:12px}
  .form-grid-3{display:grid;grid-template-columns:1fr 1fr auto;gap:12px;align-items:end}
  @media (max-width:640px){ .form-grid,.form-grid-3{grid-template-columns:1fr} }
  /* Quiz card */
  .quiz-card{border:1px solid var(--border);border-radius:12px;padding:12px;background:#fff;margin-bottom:10px}
  .quiz-head{display:flex;align-items:center;gap:10px}
  .quiz-head input[type="checkbox"]{width:18px;height:18px}
  .quiz-title{font-weight:800}
  .type-chip{margin-left:auto;font-size:12px;padding:4px 8px;border-radius:999px;background:#eef2ff}
  .opts{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-top:8px}
  .opt{display:flex;align-items:center;gap:8px;border:1px solid var(--border);border-radius:10px;padding:10px;justify-content:center}
  .opt .mark{width:28px;height:28px;display:inline-flex;align-items:center;justify-content:center;border-radius:999px;background:#eef2ff;font-size:13px;font-weight:800;flex:none}
  .opt.correct{border-color:#86efac;background:#ecfdf5}
  .opt.correct .mark{background:#d1fae5;color:#065f46}
  @media (max-width:640px){ .opts{grid-template-columns:1fr 1fr} }
  .short-ans,.ox-ans{margin-top:8px;font-size:14px}
  .evidence{margin-top:6px;font-size:12px;color:#475569}
  .footer-actions{display:flex;justify-content:flex-end;gap:10px;margin-top:16px}
  /* Settings modal */
  .modal{position:fixed;inset:0;background:rgba(0,0,0,.45);display:none;align-items:center;justify-content:center;padding:16px;z-index:50}
  .modal .panel{background:#fff;border-radius:12px;max-width:560px;width:100%;padding:16px;border:1px solid var(--border);box-shadow:0 6px 24px rgba(0,0,0,.2)}
  .row{display:flex;gap:10px;flex-wrap:wrap}
  .row>.col{flex:1 1 240px}
  #debug{position:fixed;left:16px;right:16px;bottom:16px;background:#111827;color:#fff;padding:10px 12px;border-radius:10px;box-shadow:0 6px 20px rgba(0,0,0,.25);font-size:12px;display:none;z-index:9999;white-space:pre-wrap}
  .pill{display:inline-flex;align-items:center;gap:6px;padding:6px 10px;border-radius:999px;font-size:12px;border:1px solid #ffffff55}
  .pill.red{background:#ef44441a;border-color:#ef444455}
  .pill.green{background:#16a34a1a;border-color:#16a34a55}
</style>
</head>
<body>
<header class="app">
  <h1>퀴즈 관리자</h1>
  <div class="row" style="align-items:center;gap:8px">
    <span id="keyState" class="pill red">API 키 미설정</span>
    <button class="btn ghost" id="openSettings" type="button">⚙️ 설정</button>
    <button class="btn ghost hidden" id="logoutBtn" type="button">로그아웃</button>
  </div>
</header>

<div class="container">

  <!-- 로그인 -->
  <section id="loginScreen" class="card">
    <h2 class="title">관리자 로그인</h2>
    <form id="loginForm">
      <div class="form-grid">
        <div><label class="muted">아이디</label><input id="loginId" autocomplete="username" /></div>
        <div><label class="muted">비밀번호</label><input id="loginPw" type="password" autocomplete="current-password" /></div>
      </div>
      <div class="footer-actions" style="justify-content:flex-start">
        <button class="btn primary" id="loginBtn" type="submit">로그인</button>
      </div>
    </form>
    <p class="muted">데모 계정: sbsent / momo1234</p>
  </section>

  <!-- 목록 -->
  <section id="listScreen" class="card hidden">
    <div class="toolbar">
      <h2 class="title">기사 목록</h2>
      <div class="top-actions">
        <label class="muted"><input type="checkbox" id="filterActive"> 사용 중만 보기</label>
        <button class="btn secondary" id="createBtn" type="button">퀴즈 만들기</button>
      </div>
    </div>
    <table>
      <thead><tr><th>제목</th><th>카테고리</th><th>사용</th><th>삭제</th></tr></thead>
      <tbody id="listTbody"></tbody>
    </table>
  </section>

  <!-- 생성 -->
  <section id="createScreen" class="card hidden">
    <div class="toolbar">
      <h2 class="title">퀴즈 만들기</h2>
      <button class="btn secondary" id="backFromCreate" type="button">뒤로가기</button>
    </div>

    <div class="form-grid-3">
      <div>
        <label class="muted">카테고리</label>
        <select id="createCategory">
          <option>방송</option><option>영화</option><option>뮤직</option><option>스타</option>
        </select>
      </div>
      <div>
        <label class="muted">제목</label>
        <input id="createTitle" />
      </div>
      <div>
        <div class="toggle-group">
          <div>
            <div class="switch-label">사용 여부</div>
            <div class="switch-hint">목록/서비스 노출</div>
          </div>
          <div id="createUseSwitch" class="switch on" role="switch" aria-checked="true" tabindex="0"></div>
        </div>
      </div>
    </div>

    <div class="form-grid">
      <div>
        <label class="muted">URL</label>
        <input id="createUrl" placeholder="https://..." />
      </div>
      <div>
        <label class="muted">본문 (최대 3000자)</label>
        <textarea id="createContent" maxlength="3000"></textarea>
      </div>
    </div>

    <div class="toolbar" style="margin-top:6px">
      <div class="muted">본문에서 객관식/주관식(OX 포함) 2개 생성</div>
      <div class="row" style="gap:8px;align-items:center">
        <select id="modelSelect" class="muted" style="padding:8px 10px">
          <option value="gpt-4o-mini" selected>gpt-4o-mini</option>
          <option value="gpt-4o">gpt-4o</option>
          <option value="gpt-4.1-mini">gpt-4.1-mini</option>
        </select>
        <button class="btn primary" id="createGenerate" type="button">퀴즈 2개 생성</button>
      </div>
    </div>

    <div id="createPreview"></div>
    <div class="footer-actions hidden" id="createActions">
      <button class="btn primary" id="createRegister" type="button">등록하기</button>
      <button class="btn secondary" id="createRegen" type="button">다시 생성</button>
    </div>
  </section>

  <!-- 편집 -->
  <section id="editScreen" class="card hidden">
    <div class="toolbar">
      <h2 class="title">기사 편집</h2>
      <button class="btn secondary" id="backFromEdit" type="button">뒤로가기</button>
    </div>

    <div class="form-grid-3">
      <div>
        <label class="muted">카테고리</label>
        <select id="editCategory">
          <option>방송</option><option>영화</option><option>뮤직</option><option>스타</option>
        </select>
      </div>
      <div>
        <label class="muted">제목</label>
        <input id="editTitle" />
      </div>
      <div>
        <div class="toggle-group">
          <div>
            <div class="switch-label">사용 여부</div>
            <div class="switch-hint">목록/서비스 노출</div>
          </div>
          <div id="editUseSwitch" class="switch" role="switch" aria-checked="false" tabindex="0"></div>
        </div>
      </div>
    </div>

    <div class="form-grid">
      <div>
        <label class="muted">URL</label>
        <input id="editUrl" />
      </div>
      <div>
        <label class="muted">본문</label>
        <textarea id="editContent" maxlength="3000"></textarea>
      </div>
    </div>

    <h3 class="title" style="font-size:16px;margin-top:10px">현재 적용된 퀴즈</h3>
    <div id="appliedList"></div>
    <div class="footer-actions">
      <button class="btn secondary" id="editRegenSelected" type="button">선택 퀴즈 다시 생성</button>
      <button class="btn primary" id="saveArticleBtn" type="button">저장</button>
    </div>
  </section>
</div>

<!-- 설정 모달 -->
<div id="settingsModal" class="modal" aria-hidden="true">
  <div class="panel">
    <div class="toolbar" style="margin-bottom:8px">
      <h3 class="title">설정</h3>
      <button class="btn secondary" id="closeSettings" type="button">닫기</button>
    </div>
    <div class="row">
      <div class="col">
        <label class="muted">OpenAI API Key (브라우저에 저장됩니다)</label>
        <input id="apiKeyInput" type="password" placeholder="sk-..." />
      </div>
      <div class="col">
        <label class="muted">요청 모드</label>
        <select id="modeSelect">
          <option value="direct" selected>직접 OpenAI 호출(브라우저)</option>
          <option value="proxy">사내 프록시 사용</option>
        </select>
      </div>
    </div>
    <div class="row">
      <div class="col"><label class="muted">프록시 엔드포인트 (POST JSON)</label><input id="proxyUrlInput" placeholder="https://your-proxy.company/api/generate-quiz" /></div>
    </div>
    <div class="footer-actions">
      <button class="btn primary" id="saveSettings" type="button">저장</button>
    </div>
    <p class="muted" style="margin-top:6px">※ 브라우저에서 직접 호출 시, 키가 노출될 수 있고 CORS가 차단될 수 있습니다. 운영 환경에서는 프록시 권장.</p>
  </div>
</div>

<div id="debug"></div>

<script>
(function(){
  // ----- State -----
  const VALID_ID='sbsent', VALID_PW='momo1234';
  const STORAGE = { key:'qa_api_key', mode:'qa_mode', proxy:'qa_proxy_url', articles:'qa_articles' };
  let articles = JSON.parse(localStorage.getItem(STORAGE.articles)||'[]');
  if(!articles.length){
    articles = [
      {title:"'웬즈데이' 시즌2 기자회견", category:"영화", active:true, url:"https://ent.sbs.co.kr/news/article.do?article_id=E10010304915", content:"제나 오르테가와 팀 버튼의 발언과 파트2에서 이니드 중심…", quizzes:[]},
      {title:"G-DRAGON 2025 WORLD TOUR", category:"뮤직", active:true, url:"", content:"3월 서울 시작, 아시아 각지 성황, 8월 미국·프랑스 투어 예고…", quizzes:[]}
    ];
    saveArticles();
  }
  let editingIndex = null;
  let createPreviewQuizzes = [];
  const $ = (id)=>document.getElementById(id);

  // ----- Utils -----
  function saveArticles(){ localStorage.setItem(STORAGE.articles, JSON.stringify(articles)); }
  function show(id){ ["loginScreen","listScreen","createScreen","editScreen"].forEach(x=>$(x).classList.add("hidden")); $(id).classList.remove("hidden"); }
  function bindSwitch(el, initial){ if(initial){ el.classList.add("on"); el.setAttribute("aria-checked","true"); }
    el.addEventListener("click",()=>{ el.classList.toggle("on"); el.setAttribute("aria-checked", el.classList.contains("on")?"true":"false"); });
    el.addEventListener("keydown",(e)=>{ if(e.key===" "||e.key==="Enter"){ e.preventDefault(); el.click(); } });
  }
  function setSwitch(el,on){ el.classList.toggle("on",!!on); el.setAttribute("aria-checked", on?"true":"false"); }
  function switchChecked(el){ return el.classList.contains("on"); }

  // Similarity helpers to avoid duplicate questions
  function normalizeK(txt){
    return String(txt||'').toLowerCase()
      .replace(/[\s\u00A0]+/g,'')
      .replace(/[^\p{L}\p{N}]/gu,''); // keep letters & numbers
  }
  function jaccardTokens(a,b){
    const ta = new Set(String(a||'').toLowerCase().split(/[^\\p{L}\\p{N}]+/u).filter(Boolean));
    const tb = new Set(String(b||'').toLowerCase().split(/[^\\p{L}\\p{N}]+/u).filter(Boolean));
    if(!ta.size || !tb.size) return 0;
    let inter=0; ta.forEach(t=>{ if(tb.has(t)) inter++; });
    return inter / (ta.size + tb.size - inter);
  }
  function isSimilar(q1, q2){
    const a = normalizeK(q1?.question), b = normalizeK(q2?.question);
    if(!a || !b) return false;
    if(a === b) return true;
    const jac = jaccardTokens(q1?.question, q2?.question);
    return jac >= 0.8;
  }

  // render list
  function renderList(){
    const onlyActive = $("filterActive").checked;
    const tb = $("listTbody"); tb.innerHTML = "";
    articles.filter(a=>!onlyActive || a.active).forEach((a,i)=>{
      const tr=document.createElement("tr");
      tr.innerHTML = '<td><a href="#" data-i="'+i+'" class="lnk">'+a.title+'</a><div class="muted">'+(a.url||'')+'</div></td>'
        + '<td>'+a.category+'</td>'
        + '<td>'+(a.active?'<span class="badge on">사용</span>':'<span class="badge off">미사용</span>')+'</td>'
        + '<td><button class="btn danger" data-del="'+i+'" type="button">삭제</button></td>';
      tb.appendChild(tr);
    });
    tb.querySelectorAll(".lnk").forEach(a=>a.addEventListener("click",(e)=>{e.preventDefault(); openEdit(+a.getAttribute("data-i"));}));
    tb.querySelectorAll("[data-del]").forEach(btn=>btn.addEventListener("click",()=>{ const idx=+btn.getAttribute("data-del"); if(confirm("정말 삭제하시겠습니까?")){ articles.splice(idx,1); saveArticles(); renderList(); }}));
  }

  function renderQuizCards(containerId, quizzes, withCheckbox){
    const host=$(containerId); host.innerHTML='';
    (quizzes||[]).forEach((q,i)=>{
      const card=document.createElement("div"); card.className="quiz-card";
      const head=document.createElement("div"); head.className="quiz-head";
      head.innerHTML=(withCheckbox?'<input type="checkbox" data-i="'+i+'"> ':'')+'<div class="quiz-title">Q'+(i+1)+'. '+(q.question||'(질문 없음)')+'</div>'
        + '<span class="type-chip">'+(q.type||'')+'</span>';
      card.appendChild(head);

      if(q.type==='MCQ'){
        const opts=document.createElement("div"); opts.className="opts";
        const labels=["A","B","C","D"];
        (q.options||[]).slice(0,4).forEach((opt,idx)=>{
          const row=document.createElement("div"); row.className="opt"+(idx===q.correct?' correct':'');
          row.innerHTML='<div class="mark">'+labels[idx]+'</div><div>'+ (opt||'') + '</div>';
          opts.appendChild(row);
        });
        card.appendChild(opts);
      } else if(q.type==='SHORT'){
        const box=document.createElement("div"); box.className="short-ans";
        box.innerHTML='<b>정답(단답):</b> '+(q.answer||'');
        card.appendChild(box);
      } else if(q.type==='OX'){
        // Render O/X only (no text like "참/거짓")
        const opts=document.createElement("div"); opts.className="opts";
        const ox = (String(q.answer||'').toUpperCase()==='X') ? 'X' : 'O';
        ['O','X'].forEach((label)=>{
          const row=document.createElement("div"); row.className="opt"+(ox===label?' correct':'');
          row.innerHTML='<div class="mark">'+label+'</div>';
          opts.appendChild(row);
        });
        card.appendChild(opts);
      }

      if(q.explanation){ const ev=document.createElement("div"); ev.className="evidence"; ev.innerHTML='<b>해설:</b> '+q.explanation; card.appendChild(ev); }
      if(Array.isArray(q.evidence_spans) && q.evidence_spans.length){
        const s=document.createElement("div"); s.className="evidence";
        s.innerHTML = '<b>근거 인덱스:</b> ' + q.evidence_spans.map(e => '['+e.start+','+e.end+']').join(', ');
        card.appendChild(s);
      }
      host.appendChild(card);
    });
  }
  function getCheckedIndices(containerId){ return Array.from(document.querySelectorAll("#"+containerId+" input[type=checkbox]")).filter(x=>x.checked).map(x=>+x.getAttribute("data-i")); }

  // ----- Settings -----
  function loadKey(){ return localStorage.getItem(STORAGE.key)||''; }
  function loadMode(){ return localStorage.getItem(STORAGE.mode)||'direct'; }
  function loadProxy(){ return localStorage.getItem(STORAGE.proxy)||''; }
  function refreshKeyState(){
    const has = !!loadKey();
    $("keyState").className = 'pill ' + (has?'green':'red');
    $("keyState").textContent = has ? 'API 키 설정됨' : 'API 키 미설정';
  }
  $("openSettings").addEventListener("click", ()=>{
    $("settingsModal").style.display='flex';
    $("apiKeyInput").value = loadKey();
    $("modeSelect").value = loadMode();
    $("proxyUrlInput").value = loadProxy();
  });
  $("closeSettings").addEventListener("click", ()=>{ $("settingsModal").style.display='none'; });
  $("saveSettings").addEventListener("click", ()=>{
    localStorage.setItem(STORAGE.key, $("apiKeyInput").value.trim());
    localStorage.setItem(STORAGE.mode, $("modeSelect").value);
    localStorage.setItem(STORAGE.proxy, $("proxyUrlInput").value.trim());
    refreshKeyState();
    $("settingsModal").style.display='none';
  });
  refreshKeyState();

  // ----- GPT Prompt -----
  function buildPrompt(category, content, count, avoidList){
    const avoidBlock = Array.isArray(avoidList) && avoidList.length
      ? `\n(중복 금지 기준) 아래 문항들과 질문·선지·해설이 유사하거나 중복되지 않게 구성:\n${avoidList.map(t=>'- '+t).join('\n')}\n` : '';
    return {
      system: `당신은 한국어 뉴스 기사에서 사실 기반 퀴즈를 만드는 출제자입니다.
규칙:
1) 기사 본문에 명시된 팩트만 사용. 해석·의견·추측 금지.
2) 객관식의 경우 정답은 1개, 오답 3개는 그럴듯하지만 기사와 모순 금지. 보기 문장은 짧게.
3) 질문/보기/해설은 반말 금지, 존댓말도 금지. 간결하고 명확한 평서체.
4) 정답 근거가 되는 본문 구간의 UTF-8 바이트 인덱스 배열(evidence_spans: [{start,end}])을 제공.
5) 문제 유형은 다양하게 구성: MCQ(객관식), SHORT(주관식 단답), OX 중 혼합.
6) 전체 ${count}문항. 난이도는 과하지 않게, 호기심을 자극할 적절한 수준.
7) 각 문항은 서로 다른 사실/주제에 기반. 동일하거나 유사한 질문/선지/해설 금지.
출력은 아래 JSON 스키마를 엄격히 따르세요(추가 텍스트 금지).
{
  "quizzes": [
    {
      "type": "MCQ|SHORT|OX",
      "question": "문제 문장",
      "options": ["선택지A","선택지B","선택지C","선택지D"], // MCQ에서만
      "correct": 0, // MCQ에서 0~3 정답 인덱스
      "answer": "정답(주관식 또는 OX일 경우)", // SHORT/OX에서만
      "explanation": "간결한 해설",
      "evidence_spans": [{"start":0,"end":12}]
    }
  ]
}`,
      user: `카테고리: ${category}
본문(UTF-8로 인코딩된 텍스트):
\"\"\"
${content}
\"\"\"
요청: 위 본문에서 ${count}문항 생성.${avoidBlock}`
    };
  }

  // ----- AI Caller -----
  async function callAI_GenerateQuizzes({model, content, category, count, avoidList}){
    const mode = loadMode();
    const key = loadKey();
    if(mode === 'proxy'){
      const url = loadProxy();
      if(!url) throw new Error('프록시 URL이 설정되지 않았습니다.');
      const res = await fetch(url, {
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body: JSON.stringify({ content, category, count, avoid: avoidList||[] })
      });
      if(!res.ok) throw new Error('프록시 응답 오류 '+res.status);
      const data = await res.json();
      return normalizeQuizzes(data.quizzes||data);
    } else {
      if(!key) throw new Error('API 키가 없습니다.');
      const prompt = buildPrompt(category, content, count, avoidList);
      const res = await fetch('https://api.openai.com/v1/chat/completions', {
        method:'POST',
        headers:{ 'Content-Type':'application/json','Authorization':'Bearer '+key },
        body: JSON.stringify({
          model: model || 'gpt-4o-mini',
          messages: [
            {role:'system', content: prompt.system},
            {role:'user', content: prompt.user}
          ],
          temperature: 0.7,
          response_format: { type: "json_object" }
        })
      });
      if(!res.ok){
        const t = await res.text().catch(()=>String(res.status));
        throw new Error('OpenAI 응답 오류: '+t);
      }
      const data = await res.json();
      const text = data?.choices?.[0]?.message?.content || '{}';
      let parsed;
      try{ parsed = JSON.parse(text); }catch{ throw new Error('JSON 파싱 실패'); }
      return normalizeQuizzes(parsed.quizzes||parsed);
    }
  }

  function normalizeQuizzes(qs){
    if(!Array.isArray(qs)) return [];
    return qs.map((q)=>{
      const type = (q.type||'MCQ').toUpperCase();
      const base = {
        type,
        question: String(q.question||'').trim(),
        explanation: q.explanation ? String(q.explanation) : '',
        evidence_spans: Array.isArray(q.evidence_spans) ? q.evidence_spans
          .map(e=>({start: Number(e.start)||0, end: Number(e.end)||0}))
          : []
      };
      if(type==='MCQ'){
        let opts = Array.isArray(q.options)? q.options.slice(0,4).map(s=>String(s||'')) : [];
        while(opts.length<4) opts.push('');
        const correct = Number.isInteger(q.correct)? q.correct : 0;
        return {...base, options: opts, correct};
      }else if(type==='SHORT'){
        return {...base, answer: String(q.answer||'').trim()};
      }else if(type==='OX'){
        const a = String(q.answer||'').toUpperCase();
        return {...base, answer: (a==='O' || a==='X') ? a : 'O'};
      }else{
        return {...base, type:'MCQ', options: ['','','',''], correct:0};
      }
    }).filter(q=>q.question);
  }

  function dummyQuizzes(category, content, count, avoidList){
    const mix = [
      {type:'MCQ', question:`${category} 기사 핵심은 무엇인가요?`, options:['새 발표','수상','투어 일정','논란'], correct:0, explanation:'본문 요약', evidence_spans:[{start:0,end:12}]},
      {type:'OX', question:`본문에 일정이 언급되었나요?`, answer:'O', explanation:'첫 문단', evidence_spans:[{start:13,end:24}]},
      {type:'SHORT', question:`주요 인물 이름은 무엇인가요?`, answer:'이니드', explanation:'중간 문단', evidence_spans:[{start:25,end:30}]},
      {type:'MCQ', question:`기사에서 언급된 향후 계획은 무엇인가요?`, options:['파트2 공개','활동 중단','해외 이주','휴식 선언'], correct:0, explanation:'본문 후반부', evidence_spans:[{start:31,end:44}]}
    ];
    const out=[];
    while(out.length < count){
      const q = mix[(Math.random()*mix.length)|0];
      if(!(avoidList||[]).some(t=>normalizeK(t)===normalizeK(q.question)) &&
         !out.some(x=> normalizeK(x.question)===normalizeK(q.question))){
        out.push(JSON.parse(JSON.stringify(q)));
      }
    }
    return out;
  }

  async function ensureNoDuplicateBatch(content, category, model, quizzes){
    if(quizzes.length < 2) return quizzes;
    let [q1, q2] = quizzes;
    const attempts = 3;
    let tries = 0;
    while(isSimilar(q1, q2) && tries < attempts){
      tries++;
      try{
        const fresh = await callAI_GenerateQuizzes({
          model, content, category, count:1, avoidList:[q1.question]
        });
        q2 = fresh[0] || q2;
      }catch{
        q2 = dummyQuizzes(category, content, 1, [q1.question])[0];
      }
    }
    return [q1, q2];
  }

  // ----- Screens -----
  function openEdit(i){
    editingIndex = i;
    const a = articles[i];
    $("editCategory").value = a.category;
    $("editTitle").value = a.title;
    $("editUrl").value = a.url || '';
    $("editContent").value = a.content || '';
    setSwitch($("editUseSwitch"), !!a.active);
    renderQuizCards("appliedList", a.quizzes||[], true);
    show("editScreen");
  }
  window.openEdit = openEdit;

  // ----- Events -----
  // Global delegation for back buttons (robust fix)
  document.addEventListener("click", function(e){
    if(e.target.closest("#backFromCreate")){
      e.preventDefault();
      show("listScreen");
    }
    if(e.target.closest("#backFromEdit")){
      e.preventDefault();
      show("listScreen");
    }
  });

  // Login
  $("loginForm").addEventListener("submit", (e)=>{
    e.preventDefault();
    const id=$("loginId").value.trim(), pw=$("loginPw").value.trim();
    if(id===VALID_ID && pw===VALID_PW){
      $("logoutBtn").classList.remove("hidden");
      show("listScreen"); renderList();
    } else alert("아이디 또는 비밀번호가 잘못되었습니다.");
  });
  $("logoutBtn").addEventListener("click", ()=>{
    $("logoutBtn").classList.add("hidden");
    show("loginScreen");
  });

  // List
  $("filterActive").addEventListener("change", renderList);
  $("createBtn").addEventListener("click", ()=>{
    $("createCategory").value="방송";
    $("createTitle").value=""; $("createUrl").value=""; $("createContent").value="";
    setSwitch($("createUseSwitch"), true);
    $("createPreview").innerHTML=""; $("createActions").classList.add("hidden");
    show("createScreen");
  });

  // Switches
  bindSwitch($("createUseSwitch"), true);
  bindSwitch($("editUseSwitch"), false);

  // Create: Generate via AI with no-duplicate enforcement
  $("createGenerate").addEventListener("click", async ()=>{
    const content = $("createContent").value.trim();
    if(!content){ alert("본문을 입력하세요"); return; }
    $("createGenerate").disabled = true; $("createGenerate").textContent = "생성 중...";
    const model = $("modelSelect").value;
    try{
      let quizzes = await callAI_GenerateQuizzes({model, content, category:$("createCategory").value, count:2});
      if(!quizzes.length) throw new Error('빈 결과');
      quizzes = await ensureNoDuplicateBatch(content, $("createCategory").value, model, quizzes);
      createPreviewQuizzes = quizzes;
    }catch(err){
      console.warn(err);
      createPreviewQuizzes = dummyQuizzes($("createCategory").value, content, 2);
    }finally{
      renderQuizCards("createPreview", createPreviewQuizzes, true);
      $("createActions").classList.remove("hidden");
      $("createGenerate").disabled = false; $("createGenerate").textContent = "퀴즈 2개 생성";
    }
  });

  // Create: register & regen (regen keeps no-dup within current preview)
  $("createRegister").addEventListener("click", ()=>{
    const selected = getCheckedIndices("createPreview");
    if(!selected.length){ alert("등록할 퀴즈를 선택하세요"); return; }
    const a = {
      title: $("createTitle").value || $("createContent").value.slice(0,30),
      category: $("createCategory").value,
      active: switchChecked($("createUseSwitch")),
      url: $("createUrl").value,
      content: $("createContent").value,
      quizzes: selected.map(i=>createPreviewQuizzes[i])
    };
    articles.push(a); saveArticles(); show("listScreen"); renderList();
  });

  $("createRegen").addEventListener("click", async ()=>{
    const selected = getCheckedIndices("createPreview");
    if(!selected.length){ alert("다시 생성할 퀴즈를 선택하세요"); return; }
    const content = $("createContent").value.trim();
    const model = $("modelSelect").value;
    $("createRegen").disabled = true; $("createRegen").textContent = "재생성 중...";
    try{
      const avoid = createPreviewQuizzes
        .filter((_,i)=>!selected.includes(i))
        .map(q=>q.question);
      let fresh = await callAI_GenerateQuizzes({model, content, category:$("createCategory").value, count:selected.length, avoidList:avoid});
      if(!fresh.length) throw new Error('빈 결과');
      if(fresh.length>1 && isSimilar(fresh[0], fresh[1])){
        const fix = await callAI_GenerateQuizzes({model, content, category:$("createCategory").value, count:1, avoidList:[fresh[0].question]});
        if(fix && fix[0]) fresh[1] = fix[0];
      }
      selected.forEach((idx,k)=>{ createPreviewQuizzes[idx] = fresh[k]; });
    }catch(err){
      console.warn(err);
      selected.forEach((idx)=>{
        const avoidQs = createPreviewQuizzes.filter((_,i)=>i!==idx).map(q=>q.question);
        const repl = dummyQuizzes($("createCategory").value, content, 1, avoidQs)[0];
        createPreviewQuizzes[idx] = repl;
      });
    }finally{
      renderQuizCards("createPreview", createPreviewQuizzes, true);
      $("createRegen").disabled = false; $("createRegen").textContent = "다시 생성";
    }
  });

  // Edit: regen selected & save (no-dup with remaining applied)
  $("editRegenSelected").addEventListener("click", async ()=>{
    const a = articles[editingIndex];
    const selected = getCheckedIndices("appliedList");
    if(!selected.length){ alert("다시 생성할 퀴즈를 선택하세요"); return; }
    $("editRegenSelected").disabled = true; $("editRegenSelected").textContent = "재생성 중...";
    try{
      const avoid = a.quizzes.filter((_,i)=>!selected.includes(i)).map(q=>q.question);
      let fresh = await callAI_GenerateQuizzes({model: $("modelSelect").value, content:a.content, category:a.category, count:selected.length, avoidList:avoid});
      if(!fresh.length) throw new Error('빈 결과');
      if(fresh.length>1 && isSimilar(fresh[0], fresh[1])){
        const fix = await callAI_GenerateQuizzes({model:$("modelSelect").value, content:a.content, category:a.category, count:1, avoidList:[fresh[0].question]});
        if(fix && fix[0]) fresh[1] = fix[0];
      }
      selected.forEach((idx,k)=>{ a.quizzes[idx] = fresh[k]; });
      saveArticles();
    }catch(err){
      console.warn(err);
      selected.forEach((idx)=>{
        const avoidQs = a.quizzes.filter((_,i)=>i!==idx).map(q=>q.question);
        a.quizzes[idx] = dummyQuizzes(a.category, a.content, 1, avoidQs)[0];
      });
      saveArticles();
    }finally{
      renderQuizCards("appliedList", a.quizzes, true);
      $("editRegenSelected").disabled = false; $("editRegenSelected").textContent = "선택 퀴즈 다시 생성";
    }
  });

  $("saveArticleBtn").addEventListener("click", ()=>{
    const a = articles[editingIndex];
    a.title = $("editTitle").value;
    a.category = $("editCategory").value;
    a.url = $("editUrl").value;
    a.content = $("editContent").value;
    a.active = switchChecked($("editUseSwitch"));
    saveArticles(); alert("저장되었습니다."); show("listScreen"); renderList();
  });

  // Init view
  $("logoutBtn").classList.add("hidden");
  show("loginScreen");
})();
</script>
</body>
</html>
