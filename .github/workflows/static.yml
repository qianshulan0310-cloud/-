<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>四回合暧昧判断器 V3（更细动作 + 补位指数）</title>
  <style>
    :root{
      --bg:#0b0f14; --card:#111826; --text:#e8eef7; --muted:#9fb0c3;
      --acc:#4ea1ff; --bad:#ff5a5f; --good:#2bd97f; --warn:#ffd166;
      --line: rgba(255,255,255,.06);
    }
    body{ margin:0; font-family: ui-sans-serif, system-ui, -apple-system, "PingFang SC", "Microsoft YaHei"; background:var(--bg); color:var(--text); }
    .wrap{ max-width:1120px; margin:0 auto; padding:18px; }
    h1{ font-size:20px; margin:0 0 8px; }
    .sub{ color:var(--muted); font-size:13px; line-height:1.45; }
    .grid{ display:grid; gap:14px; grid-template-columns: 1.25fr 0.75fr; margin-top:14px; }
    .card{ background:var(--card); border:1px solid rgba(255,255,255,.06); border-radius:14px; padding:14px; }
    textarea{
      width:100%; height:340px; background:#0f1623; color:var(--text);
      border:1px solid rgba(255,255,255,.08); border-radius:12px; padding:12px;
      resize:vertical; line-height:1.5; font-size:13px;
    }
    .row{ display:flex; gap:10px; flex-wrap:wrap; align-items:center; }
    button{ background:var(--acc); color:#04101f; border:0; border-radius:10px; padding:10px 12px; font-weight:800; cursor:pointer; }
    button.secondary{ background:#223049; color:var(--text); font-weight:700; }
    button.ghost{ background:transparent; border:1px solid rgba(255,255,255,.12); color:var(--text); }
    .pill{ display:inline-flex; gap:8px; align-items:center; padding:6px 10px; border-radius:999px; background:rgba(255,255,255,.06); color:var(--muted); font-size:12px; }
    .score{ font-size:46px; font-weight:950; letter-spacing:-1px; }
    .hint{ color:var(--muted); font-size:13px; }
    .k{ color:var(--muted); width:190px; display:inline-block; }
    .select{ background:#0f1623; border:1px solid rgba(255,255,255,.08); border-radius:10px; padding:8px 10px; color:var(--text); }
    ul{ margin:8px 0 0; padding-left:18px; color:var(--muted); }
    .divider{ height:1px; background:var(--line); margin:12px 0; }
    .mini{ font-size:12px; color:var(--muted); }
    code{ background:rgba(255,255,255,.06); padding:2px 6px; border-radius:8px; }
    .good{ color:var(--good); }
    .bad{ color:var(--bad); }
    .warn{ color:var(--warn); }
    label.chk{ display:flex; align-items:center; gap:8px; color:var(--muted); font-size:12px; }
    input[type="checkbox"]{ transform: translateY(1px); }

    .box{ background:#0f1623; border:1px solid rgba(255,255,255,.08); border-radius:12px; padding:10px; }
    .metricGrid{ display:grid; grid-template-columns:1fr 1fr; gap:10px; }
    .metric{ padding:10px; border:1px solid rgba(255,255,255,.08); border-radius:12px; background:#0f1623; }
    .metric .t{ font-size:12px; color:var(--muted); }
    .metric .v{ font-size:18px; font-weight:900; margin-top:4px; }
    .actions{ background:#0f1623; border:1px solid rgba(255,255,255,.08); border-radius:12px; padding:10px; }
    .actions h3{ margin:0 0 8px; font-size:13px; color:var(--muted); }
    .actions ol, .actions ul{ margin:6px 0 0; }
    .actions li{ margin:4px 0; }
    @media (max-width:980px){ .grid{ grid-template-columns:1fr; } .k{ width:160px; } .metricGrid{ grid-template-columns:1fr; } }
  </style>
</head>

<body>
<div class="wrap">
  <h1>四回合暧昧判断器 V3（更细动作 + 补位指数）</h1>
  <div class="sub">
    只做“下一步动作建议”（推进/保持/收手），不做“她是不是喜欢你”的断言。<br>
    建议粘贴最近 <b>8–12条</b>消息。最好格式：<code>我:</code> / <code>她:</code>（也支持<code>他/对方/你:</code>）。<br>
    如果你粘贴的是“无前缀纯文本”，勾选“按交替归属”会更准。
  </div>

  <div class="grid">
    <!-- Left -->
    <div class="card">
      <div class="row" style="justify-content:space-between;">
        <div class="pill">自动分析 + 可手动校准</div>
        <div class="row">
          <button class="secondary" id="btnDemo">填入示例</button>
          <button class="ghost" id="btnClear">清空</button>
          <button id="btnAnalyze">分析</button>
        </div>
      </div>

      <div class="row" style="margin-top:10px;">
        <label class="chk">
          <input type="checkbox" id="altAssign">
          没写“我/她:”的行，按交替归属
        </label>
        <span class="mini">适合直接复制聊天但没前缀的情况。</span>
      </div>

      <div style="margin-top:10px;">
        <textarea id="input" placeholder="粘贴最近聊天…
示例：
她: 你今天很早啊
我: 说到做到嘛
她: 哈哈哈
我: 周六有空吗？
她: 我好像有事
我: 行 那先不定
她: 我要没事就找你
她: 目前是有点事"></textarea>
      </div>

      <div class="divider"></div>

      <div class="row">
        <div class="pill">手动校准（全中文、通俗版）</div>
        <span class="mini">自动不准时再调；一般只调 1–2 项。</span>
      </div>

      <div style="margin-top:10px; display:grid; gap:10px;">
        <div class="row">
          <span class="k">① 谁更主动（发起）</span>
          <select class="select" id="mInit">
            <option value="-1">自动判断</option>
            <option value="0">0：基本都是我先找 / 我在带节奏</option>
            <option value="1">1：有来有往 / 她偶尔主动一句</option>
            <option value="2">2：她也会主动开话题/先找我</option>
          </select>
        </div>

        <div class="row">
          <span class="k">② 她回得“有内容”吗</span>
          <select class="select" id="mEng">
            <option value="-1">自动判断</option>
            <option value="0">0：偏敷衍（嗯/好/哈哈），不扩展</option>
            <option value="1">1：正常回复，但不太延伸</option>
            <option value="2">2：会补细节/发图/接梗/主动延展</option>
          </select>
        </div>

        <div class="row">
          <span class="k">③ 她有没有“补位”（行动）</span>
          <select class="select" id="mInv">
            <option value="-1">自动判断</option>
            <option value="0">0：只有口头/回应，没有行动</option>
            <option value="1">1：有意向（可以/改天/到时候），但没落地</option>
            <option value="2">2：有落地动作（定时间/订位/请客/主动约）</option>
          </select>
        </div>

        <div class="row">
          <span class="k">④ 边界是否清晰（忙但回流）</span>
          <select class="select" id="mBnd">
            <option value="-1">自动判断</option>
            <option value="0">0：反复模糊（到时候看/再说）且没回流</option>
            <option value="1">1：忙/累/有事，但属于合理边界</option>
            <option value="2">2：忙但会回流（晚点/明天/忙完且真的回来）</option>
          </select>
        </div>
      </div>

      <div class="divider"></div>
      <div class="mini">
        评分解释：0–2 收手｜3–5 保持｜6–8 推进。<br>
        “推进”仅指：做一次低压力落地（给2个选项、让对方定一次），不是表白。
      </div>
    </div>

    <!-- Right -->
    <div class="card">
      <div class="row" style="justify-content:space-between;">
        <div class="pill">结果</div>
        <div class="row">
          <div class="pill" id="tag">等待分析</div>
          <button class="secondary" id="btnCopy">复制结论</button>
        </div>
      </div>

      <div style="margin-top:10px;">
        <div class="score" id="score">–</div>
        <div class="hint" id="action">输入聊天并点击“分析”</div>
      </div>

      <div class="divider"></div>

      <div class="row"><div class="pill">分项（0/1/2）</div></div>
      <div style="margin-top:8px; font-size:14px;">
        <div><span class="k">发起</span><span id="sInit">–</span></div>
        <div><span class="k">承接</span><span id="sEng">–</span></div>
        <div><span class="k">补位</span><span id="sInv">–</span></div>
        <div><span class="k">边界</span><span id="sBnd">–</span></div>
      </div>

      <div class="divider"></div>

      <div class="row"><div class="pill">补位指数（更通用统计）</div></div>
      <div class="metricGrid" style="margin-top:10px;">
        <div class="metric">
          <div class="t">补位指数（0–100）</div>
          <div class="v" id="mIndex">–</div>
          <div class="mini" id="mIndexHint">（分析后显示）</div>
        </div>
        <div class="metric">
          <div class="t">对方主动占比（估算）</div>
          <div class="v" id="mInitRatio">–</div>
          <div class="mini" id="mInitRatioHint">（对方更常开新回合的比例）</div>
        </div>
        <div class="metric">
          <div class="t">对方提问率</div>
          <div class="v" id="mQRate">–</div>
          <div class="mini">（对方消息里带问号的比例）</div>
        </div>
        <div class="metric">
          <div class="t">模糊率 / 回流率</div>
          <div class="v" id="mVagueBack">–</div>
          <div class="mini">（到时候看等 / 忙但回来的比例）</div>
        </div>
      </div>

      <div class="divider"></div>

      <div class="actions" id="actionsBox">
        <h3>下一步怎么做（更细动作）</h3>
        <div class="mini">（分析后生成：你只按规则做，不靠话术硬撑）</div>
      </div>

      <div class="divider"></div>

      <div class="row"><div class="pill">自动检测到的线索</div></div>
      <ul id="signals"></ul>

      <div class="divider"></div>

      <div class="row"><div class="pill">红灯 / 绿灯</div></div>
      <ul id="flags"></ul>

      <div class="divider"></div>
      <div class="mini" id="copyText">（分析后这里会生成可复制总结）</div>
    </div>
  </div>
</div>

<script>
  const $ = (id)=>document.getElementById(id);

  const patterns = {
    land: /(我请|我来请|我定|我订|我安排|我来接|我去找你|我过去|下次我请|我做给你|你定时间|你定个时间|你挑个时间|我们约|约一下|见一面|见面|一起吃|一起喝|一起去|周末|周[一二三四五六日天]|周五|明天|今晚|后天|几点|几号|哪天|地点|订位|买票)/i,
    maybe: /(可以啊|行啊|好啊|改天|下次|以后|回头|再说|到时候|看情况|我看看|有空|不一定)/i,
    vague: /(到时候|看情况|我看看|再说|改天|回头|不一定)/i,
    busy: /(在忙|忙死|有事|处理|加班|累|困|要睡|回家|路上|上班|开会|上课|带班|不方便|今天不行|现在不行)/i,
    back: /(晚点(再)?说|晚点找你|忙完(再)?说|忙完找你|明天(再)?说|到家(再)?说|回头我找你|等我一下)/i,
    media: /(\[图片\]|\[视频\]|\[语音\]|图片|视频|拍照|截图|发你|给你看|表情包)/i,
    laugh: /(哈哈|hhh|笑死|😂|😅|🤣)/i,
    question: /[？?]/,
    lowReply: /^(嗯|哦|好|行|可以|ok|哈|哈哈|嗯嗯|好的|收到|是滴|对|哈哈哈)$/i
  };

  function parseLines(text, useAlternate=false){
    const lines = text.split(/\n+/).map(s=>s.trim()).filter(Boolean);
    const msgs = [];
    let alt = 0; // 0->对方, 1->我
    for(const ln of lines){
      const m = ln.match(/^(\s*(我|你|她|他|对方)\s*[:：]\s*)(.*)$/);
      if(m){
        msgs.push({who:m[2], text:(m[3]||"").trim()});
      } else if(useAlternate){
        msgs.push({who: alt===0 ? "对方" : "我", text:ln});
        alt = 1-alt;
      } else {
        msgs.push({who:"?", text:ln});
      }
    }
    return msgs;
  }

  function isOther(w){ return (w==="她"||w==="他"||w==="对方") }
  function isMe(w){ return (w==="我"||w==="你") }

  function feat(t){
    const s = (t||"").trim();
    return {
      len: s.length,
      q: patterns.question.test(s),
      land: patterns.land.test(s),
      maybe: patterns.maybe.test(s),
      vague: patterns.vague.test(s),
      busy: patterns.busy.test(s),
      back: patterns.back.test(s),
      media: patterns.media.test(s),
      laugh: patterns.laugh.test(s),
      low: patterns.lowReply.test(s)
    }
  }

  function clamp(n,min,max){ return Math.max(min, Math.min(max,n)); }
  function pct(x){ return (Math.round(x*100)) + "%"; }

  // 估算“对方主动开新回合占比”：对方消息中，带问号/落地词/回流承诺视作“主动动作”
  function estimateInitiativeRatio(other){
    if(other.length===0) return 0;
    const active = other.filter(m=>m.f.q || m.f.land || m.f.back).length;
    return active/other.length;
  }

  function analyze(text){
    const msgs = parseLines(text, $("altAssign").checked);

    const other = [];
    const me = [];

    for(const m of msgs){
      const f = feat(m.text);
      if(isOther(m.who) || m.who==="对方") other.push({...m,f});
      else if(isMe(m.who) || m.who==="我") me.push({...m,f});
    }

    const signals = [];
    const flags = [];

    // ① 发起
    let init = 0;
    const first = msgs.find(m => (m.who!=="?") && m.text.length>0);
    if(first && (isOther(first.who)||first.who==="对方")) init = 2;
    const otherQ = other.filter(m=>m.f.q).length;
    if(otherQ>=1) init = Math.max(init,1);
    if(otherQ>=3) init = 2;

    // ② 承接
    let eng = 0;
    let avgLen = 0, mediaCnt=0, laughCnt=0, lowCnt=0;
    if(other.length>0){
      avgLen = other.reduce((a,m)=>a+m.f.len,0)/other.length;
      mediaCnt = other.filter(m=>m.f.media).length;
      laughCnt = other.filter(m=>m.f.laugh).length;
      lowCnt   = other.filter(m=>m.f.low).length;

      if(lowCnt/other.length >= 0.6 && avgLen <= 6) eng = 0;
      else eng = 1;
      if(avgLen >= 18 || mediaCnt>=1 || (laughCnt>=2 && avgLen>=10)) eng = 2;
    }

    // ③ 补位
    let inv = 0;
    const landCnt = other.filter(m=>m.f.land).length;
    const maybeCnt= other.filter(m=>m.f.maybe).length;
    if(landCnt>=1) inv = 2;
    else if(maybeCnt>=1) inv = 1;
    else inv = 0;

    // ④ 边界
    let bnd = 1;
    const vagueCnt = other.filter(m=>m.f.vague).length;
    const busyCnt  = other.filter(m=>m.f.busy).length;
    const backCnt  = other.filter(m=>m.f.back).length;

    if(vagueCnt>=2 && landCnt===0 && backCnt===0) bnd = 0;
    else if(busyCnt>=1 && backCnt>=1) bnd = 2;
    else if(busyCnt>=1) bnd = 1;
    else bnd = 1;

    // signals
    if(first && (isOther(first.who)||first.who==="对方")) signals.push("对方先开口（主动信号）");
    if(otherQ>0) signals.push(`对方提问 ${otherQ} 次（在延续互动）`);
    if(landCnt>0) signals.push("出现落地词（定时间/约见面/请客/安排等）");
    if(maybeCnt>0 && landCnt===0) signals.push("出现意向词（可以/改天/到时候看），但未落地");
    if(mediaCnt>0) signals.push(`对方发图/语音/表情等 ${mediaCnt} 次（承接更强）`);
    if(vagueCnt>0) signals.push(`出现模糊词 ${vagueCnt} 次（到时候/再说/我看看）`);
    if(busyCnt>0) signals.push("出现忙/累/困/有事（边界表达）");
    if(backCnt>0) signals.push("出现回流承诺（晚点/明天/忙完找你）");

    // flags
    if(vagueCnt>=2 && landCnt===0) flags.push("🔴 红灯：连续模糊（到时候看/再说）但无落地");
    if(eng===0) flags.push("🔴 红灯：承接偏弱（短回多/不扩展）");
    if(landCnt>=1) flags.push("🟢 绿灯：对方有补位/落地动作");
    if(bnd===2) flags.push("🟢 绿灯：忙但会回流（可持续）");

    // Additional metrics
    const qRate = other.length ? otherQ/other.length : 0;
    const vagueRate = other.length ? vagueCnt/other.length : 0;
    const backRate = busyCnt ? backCnt/busyCnt : (backCnt>0?1:0);
    const initRatio = estimateInitiativeRatio(other); // 0..1
    const engageRate = other.length ? (other.length - lowCnt)/other.length : 0;

    // 补位指数（0-100）= 主动(30) + 承接有效(30) + 落地(25) + 回流(15) - 模糊惩罚(0..20)
    const landScore = landCnt>0 ? 1 : 0;
    let index = 0;
    index += 30*initRatio;
    index += 30*engageRate;
    index += 25*landScore;
    index += 15*clamp(backRate,0,1);
    index -= 20*clamp(vagueRate,0,1);
    index = clamp(Math.round(index), 0, 100);

    return {
      init, eng, inv, bnd,
      signals, flags,
      metrics: { index, initRatio, qRate, vagueRate, backRate, landCnt, maybeCnt, busyCnt, backCnt, otherCount: other.length, meCount: me.length, avgLen }
    };
  }

  function applyManual(auto){
    const mInit = parseInt($("mInit").value,10);
    const mEng  = parseInt($("mEng").value,10);
    const mInv  = parseInt($("mInv").value,10);
    const mBnd  = parseInt($("mBnd").value,10);

    return {
      init: mInit>=0 ? mInit : auto.init,
      eng : mEng>=0 ? mEng : auto.eng,
      inv : mInv>=0 ? mInv : auto.inv,
      bnd : mBnd>=0 ? mBnd : auto.bnd,
      signals: auto.signals,
      flags: auto.flags,
      metrics: auto.metrics
    };
  }

  function verdict(total){
    if(total<=2) return {tag:"收手", cls:"bad",
      action:"建议：不推进、不约、不解释；只保持礼貌回应。",
      steps:[
        "只回应她抛来的问题/信息，不主动开新话题（至少48小时）",
        "不再提见面/做饭/时间安排（避免单边驱动）",
        "把注意力放回自己的生活节奏"
      ],
      avoid:[
        "别连发/别解释空白/别追问原因",
        "别连续换日期去约（周六不行→周日→下周…）"
      ]
    };
    if(total<=5) return {tag:"保持", cls:"warn",
      action:"建议：轻聊即可，不加码（不连发/不追问/不反复提见面）。",
      steps:[
        "24–48小时内最多主动1次；每次只抛一个点",
        "她说忙/累/有事时：一句收口，不追问",
        "等待她补位：主动找你/落实一次计划/承担一次成本"
      ],
      avoid:[
        "别把聊天变成采访（午休/通勤/工作制度连环问）",
        "别反复提同一件事（做饭/周末）逼她表态"
      ]
    };
    return {tag:"推进", cls:"good",
      action:"建议：做一次低压力落地（给2个选项，让对方定一次）。",
      steps:[
        "只推进一次：给两个选项（周五晚/周末下午）让她选",
        "把成本分出去：她负责饮料/甜点/订位中的一个",
        "她若仍模糊（到时候看）：立即收口，至少5–7天不再提"
      ],
      avoid:[
        "别在推进后连环追问（几点/在哪里/你到底行不行）",
        "别把推进升级为表白或关系定义"
      ]
    };
  }

  function render(res){
    const total = res.init + res.eng + res.inv + res.bnd;
    $("score").textContent = total.toString();
    $("sInit").textContent = res.init;
    $("sEng").textContent  = res.eng;
    $("sInv").textContent  = res.inv;
    $("sBnd").textContent  = res.bnd;

    const v = verdict(total);
    $("tag").textContent = v.tag;
    $("tag").className = "pill " + v.cls;
    $("action").textContent = v.action;

    // Metrics
    const m = res.metrics || {};
    $("mIndex").textContent = (m.index ?? "–");
    $("mIndexHint").textContent = (m.index!=null)
      ? (m.index>=70 ? "高：对方在补位/回流/落地方面表现较强"
        : m.index>=45 ? "中：互动OK，但落地/补位可能不足"
        : "低：容易变成你单边驱动，建议降频或做结构测试")
      : "（分析后显示）";

    $("mInitRatio").textContent = (m.initRatio!=null) ? pct(m.initRatio) : "–";
    $("mQRate").textContent = (m.qRate!=null) ? pct(m.qRate) : "–";

    const vagueTxt = (m.vagueRate!=null) ? pct(m.vagueRate) : "–";
    const backTxt  = (m.backRate!=null)  ? pct(m.backRate)  : "–";
    $("mVagueBack").textContent = `${vagueTxt} / ${backTxt}`;

    $("mInitRatioHint").textContent = (m.otherCount!=null)
      ? `（基于对方${m.otherCount}条消息：问号/落地/回流等“主动动作”的占比估算）`
      : "（对方更常开新回合的比例）";

    // Actions box
    const box = $("actionsBox");
    box.innerHTML =
      `<h3>下一步怎么做（更细动作）</h3>
       <div class="mini">${v.action}</div>
       <div class="divider" style="margin:10px 0;"></div>
       <div class="row"><div class="pill">你该做</div></div>
       <ol>${v.steps.map(x=>`<li>${x}</li>`).join("")}</ol>
       <div class="row" style="margin-top:8px;"><div class="pill">你别做</div></div>
       <ul>${v.avoid.map(x=>`<li>${x}</li>`).join("")}</ul>`;

    // signals
    const sigUl = $("signals");
    sigUl.innerHTML = "";
    (res.signals.length?res.signals:["（无明显线索：建议勾选“交替归属”或手动校准）"]).forEach(x=>{
      const li=document.createElement("li"); li.textContent=x; sigUl.appendChild(li);
    });

    // flags
    const flagsUl = $("flags");
    flagsUl.innerHTML = "";
    (res.flags.length?res.flags:["（无）"]).forEach(x=>{
      const li=document.createElement("li"); li.textContent=x; flagsUl.appendChild(li);
    });

    const summary =
`4R-SCORE=${total}/8（发起${res.init} 承接${res.eng} 补位${res.inv} 边界${res.bnd}）
结论：${v.tag}｜${v.action}
补位指数=${m.index}/100｜对方主动占比≈${pct(m.initRatio||0)}｜提问率=${pct(m.qRate||0)}｜模糊率=${pct(m.vagueRate||0)}｜回流率=${pct(m.backRate||0)}
线索：${res.signals.join("；") || "无"}
红绿灯：${res.flags.join("；") || "无"}`;
    $("copyText").textContent = summary;
    window.__summary = summary;
  }

  $("btnAnalyze").addEventListener("click", ()=>{
    const text = $("input").value || "";
    const auto = analyze(text);
    const res = applyManual(auto);
    render(res);

    localStorage.setItem("fourR_text", text);
    localStorage.setItem("fourR_alt", $("altAssign").checked ? "1":"0");
    localStorage.setItem("fourR_manual", JSON.stringify({
      mInit:$("mInit").value, mEng:$("mEng").value, mInv:$("mInv").value, mBnd:$("mBnd").value
    }));
  });

  $("btnCopy").addEventListener("click", async ()=>{
    const t = window.__summary || $("copyText").textContent || "";
    try{
      await navigator.clipboard.writeText(t);
      $("btnCopy").textContent = "已复制";
      setTimeout(()=>$("btnCopy").textContent="复制结论",900);
    }catch(e){
      alert("复制失败：浏览器可能禁止剪贴板。你可以手动选中右下角总结复制。");
    }
  });

  $("btnDemo").addEventListener("click", ()=>{
    $("input").value =
`她: 你今天很早啊
我: 说到做到嘛
她: 哈哈哈
我: 周六有空吗？
她: 我好像有事
我: 行 那先不定
她: 我要没事就找你
她: 目前是有点事`;
    $("altAssign").checked = false;
  });

  $("btnClear").addEventListener("click", ()=>{ $("input").value=""; });

  // restore
  (function(){
    const saved = localStorage.getItem("fourR_text");
    if(saved) $("input").value = saved;
    const alt = localStorage.getItem("fourR_alt");
    if(alt) $("altAssign").checked = (alt==="1");
    const m = localStorage.getItem("fourR_manual");
    if(m){
      try{
        const o = JSON.parse(m);
        $("mInit").value = o.mInit ?? "-1";
        $("mEng").value  = o.mEng  ?? "-1";
        $("mInv").value  = o.mInv  ?? "-1";
        $("mBnd").value  = o.mBnd  ?? "-1";
      }catch(e){}
    }
  })();
</script>
</body>
</html>
