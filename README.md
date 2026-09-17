
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>数据搜索工具</title>
<link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }
body { background: #f0f4f8; font-family: 'Nunito', 'Segoe UI', system-ui, sans-serif; font-size: 13px; }

.topbar { background: #0277bd; color: #fff; padding: 11px 18px; display: flex; align-items: center; gap: 10px; }
.topbar h1 { font-size: 17px; font-weight: 800; }
.topbar span { font-size: 12px; font-weight: 500; margin-left: auto; opacity: 0.85; }

/* Modal overlay */
.modal-bg {
  display: none; position: fixed; inset: 0;
  background: rgba(0,0,0,0.45); z-index: 1000;
  align-items: center; justify-content: center;
}
.modal-bg.open { display: flex; }
.modal {
  background: #fff; border-radius: 16px;
  padding: 28px; width: 90%; max-width: 640px;
  box-shadow: 0 8px 40px rgba(0,0,0,0.22);
  font-family: 'Nunito', sans-serif;
}
.modal h2 { font-size: 16px; font-weight: 800; color: #0277bd; margin-bottom: 6px; }
.modal p { font-size: 12.5px; color: #667; margin-bottom: 12px; font-weight: 600; line-height: 1.6; }
.modal textarea {
  width: 100%; height: 200px; border: 1.5px solid #c5d8e8;
  border-radius: 10px; padding: 10px 12px; font-size: 12px;
  font-family: 'Courier New', monospace; resize: vertical;
  outline: none; color: #222; background: #f5faff;
}
.modal textarea:focus { border-color: #0277bd; box-shadow: 0 0 0 3px rgba(2,119,189,0.13); }
.modal-btns { display: flex; gap: 10px; margin-top: 14px; justify-content: flex-end; }
.btn-primary {
  background: #0277bd; color: #fff; border: none; border-radius: 10px;
  padding: 8px 20px; font-size: 13px; font-weight: 700;
  cursor: pointer; font-family: inherit; transition: background 0.15s;
}
.btn-primary:hover { background: #01579b; }
.btn-secondary {
  background: #f0f4f8; color: #667; border: 1.5px solid #c5d8e8;
  border-radius: 10px; padding: 8px 20px; font-size: 13px;
  font-weight: 700; cursor: pointer; font-family: inherit;
}
.btn-secondary:hover { background: #e1f5fe; }
.sep { display: flex; align-items: center; gap: 10px; margin: 14px 0; }
.sep span { font-size: 11px; color: #aac; font-weight: 700; white-space: nowrap; }
.sep hr { flex: 1; border: none; border-top: 1px solid #e0eaf2; }
.file-label {
  display: flex; align-items: center; justify-content: center; gap: 8px;
  border: 2px dashed #81d4fa; border-radius: 10px; padding: 14px;
  cursor: pointer; color: #0277bd; font-weight: 700; font-size: 13px;
  background: #f0f9ff; transition: all 0.15s; font-family: inherit;
}
.file-label:hover { background: #e1f5fe; border-color: #0277bd; }
#fileInputModal { display: none; }
.fmt-tabs { display: flex; gap: 6px; margin-bottom: 10px; }
.fmt-tab {
  border: 1.5px solid #c5d8e8; background: #f5faff; border-radius: 8px;
  padding: 4px 14px; font-size: 12px; font-weight: 700; cursor: pointer;
  color: #667; font-family: inherit; transition: all 0.12s;
}
.fmt-tab.active { background: #0277bd; border-color: #0277bd; color: #fff; }

/* Filter bar */
.filter-bar {
  background: #fff; border-bottom: 2.5px solid #0277bd;
  padding: 12px 18px; display: flex; flex-wrap: wrap; gap: 12px;
  align-items: flex-end; position: sticky; top: 0; z-index: 200;
  box-shadow: 0 2px 8px rgba(0,0,0,0.09);
}
.fg { display: flex; flex-direction: column; gap: 4px; flex: 1; min-width: 150px; }
.fg label { font-size: 10.5px; font-weight: 800; color: #0277bd; text-transform: uppercase; letter-spacing: 0.08em; }
.fg input {
  border: 1.5px solid #c5d8e8; border-radius: 10px;
  padding: 6px 12px; font-size: 13px; color: #222;
  background: #f5faff; outline: none;
  transition: border-color 0.15s, box-shadow 0.15s;
  font-family: inherit; font-weight: 600;
}
.fg input:focus { border-color: #0277bd; box-shadow: 0 0 0 3px rgba(2,119,189,0.13); background: #fff; }
.fg input::placeholder { font-weight: 400; color: #aac4d8; }
.btn-clear {
  background: #e1f5fe; border: 1.5px solid #81d4fa; border-radius: 10px;
  padding: 6px 16px; font-size: 12.5px; color: #01579b;
  cursor: pointer; font-weight: 700; align-self: flex-end;
  transition: background 0.15s; font-family: inherit;
}
.btn-clear:hover { background: #b3e5fc; }

/* Upload bar */
.upload-bar {
  background: #f7f9fc; border-bottom: 1px solid #dde3ea;
  padding: 9px 18px; display: flex; align-items: center; gap: 12px;
}
.upload-btn {
  background: #0277bd; color: #fff; border: none; border-radius: 10px;
  padding: 6px 16px; font-size: 12.5px; font-weight: 700;
  cursor: pointer; font-family: inherit; display: inline-flex;
  align-items: center; gap: 6px; transition: background 0.15s;
}
.upload-btn:hover { background: #01579b; }
.upload-name { font-size: 12.5px; color: #0277bd; font-weight: 700; }
.stats { margin-left: auto; font-size: 12px; color: #667; font-weight: 600; }
.stats strong { color: #111; font-weight: 800; }

/* Table */
.tbl-outer { overflow: auto; max-height: calc(100vh - 165px); border-top: 1px solid #ccd6e0; }
table { border-collapse: collapse; font-size: 12.5px; min-width: 100%; background: #fff; }
thead tr th {
  background: #0277bd; color: #fff; font-weight: 800;
  padding: 8px 11px; border: 1px solid #01579b;
  white-space: nowrap; position: sticky; top: 0; z-index: 10;
  text-align: left; font-size: 12px; cursor: pointer;
  user-select: none; font-family: inherit;
}
thead tr th:hover { background: #01579b; }
th.rn, td.rn {
  background: #e1f5fe !important; color: #5580a0 !important;
  font-size: 11px; text-align: center; min-width: 36px; max-width: 36px;
  border-color: #b3e5fc !important; font-weight: 600 !important; cursor: default !important;
}
th.rn { background: #b3e5fc !important; color: #01579b !important; font-weight: 800 !important; }
tbody tr td {
  padding: 6px 11px; border: 1px solid #d0dce8; color: #2a3a4a;
  vertical-align: middle; white-space: nowrap;
  max-width: 220px; overflow: hidden; text-overflow: ellipsis; font-weight: 500;
}
tbody tr:nth-child(even) td { background: #f0f8ff; }
tbody tr:nth-child(odd) td { background: #fff; }
tbody tr:hover td { background: #dff0fb !important; }
tbody tr.selected td { background: #b3e5fc !important; }
.c-asin { font-family: 'Courier New', monospace; font-size: 11.5px; color: #0d47a1; font-weight: 700; }
.c-link a { color: #0277bd; text-decoration: underline; font-size: 12px; font-weight: 700; }
.c-img img { width: 36px; height: 36px; object-fit: cover; border: 1px solid #dde8f0; border-radius: 5px; }
.c-img .noimg { width: 36px; height: 36px; background: #eef4fb; display: inline-flex; align-items: center; justify-content: center; font-size: 9px; color: #aac; border: 1px solid #dde3ea; border-radius: 5px; }
.c-pt { background: #e0f7fa; color: #006064; padding: 2px 10px; border-radius: 12px; font-size: 11.5px; font-weight: 700; border: 1px solid #80deea; display: inline-block; }
.c-kw { color: #5c35a0; font-size: 12.5px; font-weight: 600; }
.empty-row td { text-align: center; padding: 44px; color: #b0c4d4; font-size: 13.5px; font-weight: 600; background: #fff !important; }

/* Pagination */
.pager { background: #f7f9fc; border-top: 1px solid #dde3ea; padding: 8px 18px; display: flex; align-items: center; gap: 5px; flex-wrap: wrap; }
.pg-btn { border: 1.5px solid #c5d8e8; background: #fff; border-radius: 8px; padding: 4px 12px; font-size: 12.5px; color: #334; cursor: pointer; font-family: inherit; font-weight: 700; transition: all 0.12s; }
.pg-btn:hover { background: #e1f5fe; border-color: #0277bd; color: #0277bd; }
.pg-btn.active { background: #0277bd; border-color: #0277bd; color: #fff; }
.pg-btn:disabled { opacity: 0.35; cursor: not-allowed; }
.pg-info { font-size: 12px; color: #99aabb; margin-left: 8px; font-weight: 600; }

.toast {
  position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%);
  background: #0277bd; color: #fff; padding: 10px 24px; border-radius: 20px;
  font-weight: 700; font-size: 13px; box-shadow: 0 4px 16px rgba(0,0,0,0.18);
  opacity: 0; transition: opacity 0.3s; pointer-events: none; z-index: 9999;
}
.toast.show { opacity: 1; }
</style>
</head>
<body>

<div class="topbar">
  <svg width="22" height="22" viewBox="0 0 22 22" fill="none">
    <rect width="22" height="22" rx="6" fill="rgba(255,255,255,0.2)"/>
    <circle cx="9.5" cy="9.5" r="4" stroke="white" stroke-width="1.8" fill="none"/>
    <line x1="13" y1="13" x2="18" y2="18" stroke="white" stroke-width="2" stroke-linecap="round"/>
  </svg>
  <h1>数据搜索工具</h1>
  <span id="headerCount">共 0 条记录</span>
</div>

<!-- Filter Bar -->
<div class="filter-bar">
  <div class="fg">
    <label>�� Keywords</label>
    <input type="text" id="fKw" placeholder="搜索关键词..." oninput="applyFilters()">
  </div>
  <div class="fg">
    <label>�� ASIN</label>
    <input type="text" id="fAsin" placeholder="输入 ASIN..." oninput="applyFilters()">
  </div>
  <div class="fg">
    <label>��️ PT (Product Type)</label>
    <input type="text" id="fPt" placeholder="输入 PT 类型..." oninput="applyFilters()">
  </div>
  <button class="btn-clear" onclick="clearFilters()">✕ 清除</button>
</div>

<!-- Upload Bar -->
<div class="upload-bar">
  <button class="upload-btn" onclick="openModal()">
    <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
      <path d="M7 1v8M4 4l3-3 3 3" stroke="#fff" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
      <path d="M1 11h12" stroke="#fff" stroke-width="1.8" stroke-linecap="round"/>
    </svg>
    导入数据
  </button>
  <span id="uploadName" class="upload-name"></span>
  <div class="stats">
    显示 <strong id="showCount">0</strong> / <strong id="totalCount">0</strong> 条
    &nbsp;|&nbsp; 第 <strong id="pgCur">—</strong> / <strong id="pgTotal">—</strong> 页
  </div>
</div>

<!-- Table -->
<div class="tbl-outer" id="tblOuter">
  <table>
    <thead>
      <tr>
        <th class="rn">#</th>
        <th onclick="sortBy('img_url')">图片</th>
        <th onclick="sortBy('asin')">ASIN ⇅</th>
        <th onclick="sortBy('title')">Title ⇅</th>
        <th onclick="sortBy('keywords')">Keywords ⇅</th>
        <th onclick="sortBy('product_type')">PT ⇅</th>
        <th onclick="sortBy('question')">Question ⇅</th>
        <th onclick="sortBy('ri_comment')">RI Comment ⇅</th>
        <th onclick="sortBy('comment')">Comment ⇅</th>
        <th onclick="sortBy('create_user')">Create User ⇅</th>
        <th onclick="sortBy('tt_link')">TT Link</th>
        <th onclick="sortBy('pt_qrd_link')">PT QRD Link</th>
      </tr>
    </thead>
    <tbody id="tblBody">
      <tr class="empty-row"><td colspan="12">�� 点击「导入数据」按钮加载数据</td></tr>
    </tbody>
  </table>
</div>
<div class="pager" id="pager"></div>
<div class="toast" id="toast"></div>

<!-- Import Modal -->
<div class="modal-bg" id="modalBg" onclick="bgClick(event)">
  <div class="modal">
    <h2>�� 导入数据</h2>
    <p>方式一：直接选择本地文件（CSV / TSV / JSON）</p>

    <label class="file-label" for="fileInputModal">
      <svg width="18" height="18" viewBox="0 0 18 18" fill="none">
        <path d="M9 2v10M5 6l4-4 4 4" stroke="#0277bd" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        <path d="M2 14h14" stroke="#0277bd" stroke-width="2" stroke-linecap="round"/>
      </svg>
      点击选择文件
    </label>
    <input type="file" id="fileInputModal" accept=".csv,.tsv,.json">

    <div class="sep"><hr><span>或者</span><hr></div>

    <p>方式二：把 CSV / TSV / JSON 内容直接粘贴到下方文本框</p>
    <div class="fmt-tabs">
      <button class="fmt-tab active" onclick="setFmt('csv',this)">CSV</button>
      <button class="fmt-tab" onclick="setFmt('tsv',this)">TSV</button>
      <button class="fmt-tab" onclick="setFmt('json',this)">JSON</button>
    </div>
    <textarea id="pasteArea" placeholder="在此粘贴数据内容..."></textarea>

    <div class="modal-btns">
      <button class="btn-secondary" onclick="closeModal()">取消</button>
      <button class="btn-primary" onclick="loadData()">✓ 确认导入</button>
    </div>
  </div>
</div>

<script>
var allData=[], filteredData=[], currentPage=1, PAGE=50, sortCol='', sortAsc=true, fmt='csv';

// ── File input (modal) ──
document.getElementById('fileInputModal').onchange = function(e) {
  var file = e.target.files;
  if (!file) return;
  var reader = new FileReader();
  reader.onload = function(ev) {
    var txt = ev.target.result;
    var ext = file.name.split('.').pop().toLowerCase();
    try {
      if (ext==='json') { allData=JSON.parse(txt); if(!Array.isArray(allData)) allData=[allData]; }
      else { allData=parseDelimited(txt, ext==='tsv'?'\t':','); }
      document.getElementById('uploadName').textContent = '✓ '+file.name;
      closeModal();
      applyFilters();
      showToast('✓ 已加载 '+allData.length+' 条数据');
    } catch(err) { showToast('❌ 解析失败: '+err.message); }
  };
  reader.onerror = function(){ showToast('❌ 读取失败，请用粘贴方式'); };
  reader.readAsText(file,'UTF-8');
};

function setFmt(f, btn) {
  fmt = f;
  document.querySelectorAll('.fmt-tab').forEach(function(b){ b.classList.remove('active'); });
  btn.classList.add('active');
  var ph = f==='json' ? '[\n  {"asin":"B001","title":"示例","keywords":"test"}\n]'
         : f==='tsv'  ? 'asin\ttitle\tkeywords\nB001\t示例\ttest'
                      : 'asin,title,keywords\nB001,示例,test';
  document.getElementById('pasteArea').placeholder = ph;
}

function loadData() {
  var txt = document.getElementById('pasteArea').value.trim();
  if (!txt) { showToast('⚠️ 请先选择文件或粘贴数据'); return; }
  try {
    if (fmt==='json') { allData=JSON.parse(txt); if(!Array.isArray(allData)) allData=[allData]; }
    else { allData=parseDelimited(txt, fmt==='tsv'?'\t':','); }
    document.getElementById('uploadName').textContent = '✓ 已粘贴数据';
    closeModal();
    applyFilters();
    showToast('✓ 已加载 '+allData.length+' 条数据');
  } catch(err) { showToast('❌ 解析失败: '+err.message); }
}

function openModal() {
  document.getElementById('modalBg').classList.add('open');
  document.getElementById('pasteArea').value='';
  document.getElementById('fileInputModal').value='';
}
function closeModal() { document.getElementById('modalBg').classList.remove('open'); }
function bgClick(e) { if(e.target===document.getElementById('modalBg')) closeModal(); }

function parseDelimited(txt,sep) {
  var lines=txt.split(/\r?\n/).filter(function(l){return l.trim();});
  if(lines.length<2) return [];
  var hdrs=parseLine(lines,sep).map(function(h){return h.trim().toLowerCase().replace(/\s+/g,'_');});
  return lines.slice(1).map(function(line){
    var vals=parseLine(line,sep), obj={};
    hdrs.forEach(function(h,i){obj[h]=(vals[i]||'').trim();});
    return obj;
  });
}
function parseLine(line,sep) {
  var res=[],cur='',inQ=false;
  for(var i=0;i<line.length;i++){
    var c=line[i];
    if(c==='"'){if(inQ&&line[i+1]==='"'){cur+='"';i++;}else inQ=!inQ;}
    else if(c===sep&&!inQ){res.push(cur);cur='';}
    else cur+=c;
  }
  res.push(cur); return res;
}

function applyFilters() {
  var kw=document.getElementById('fKw').value.trim().toLowerCase();
  var asin=document.getElementById('fAsin').value.trim().toLowerCase();
  var pt=document.getElementById('fPt').value.trim().toLowerCase();
  filteredData=allData.filter(function(r){
    if(kw&&!(r.keywords||'').toLowerCase().includes(kw)) return false;
    if(asin&&!(r.asin||'').toLowerCase().includes(asin)) return false;
    if(pt&&!(r.product_type||r.pt||'').toLowerCase().includes(pt)) return false;
    return true;
  });
  if(sortCol) doSort();
  currentPage=1; render();
}
function clearFilters(){
  document.getElementById('fKw').value='';
  document.getElementById('fAsin').value='';
  document.getElementById('fPt').value='';
  applyFilters();
}
function sortBy(col){
  if(sortCol===col) sortAsc=!sortAsc; else{sortCol=col;sortAsc=true;}
  doSort(); currentPage=1; render();
}
function doSort(){
  filteredData.sort(function(a,b){
    var va=(a[sortCol]||'').toLowerCase(), vb=(b[sortCol]||'').toLowerCase();
    return sortAsc?va.localeCompare(vb):vb.localeCompare(va);
  });
}

function render(){
  var total=filteredData.length;
  var pages=Math.max(1,Math.ceil(total/PAGE));
  if(currentPage>pages) currentPage=pages;
  var start=(currentPage-1)*PAGE, slice=filteredData.slice(start,start+PAGE);
  document.getElementById('totalCount').textContent=allData.length;
  document.getElementById('showCount').textContent=total;
  document.getElementById('pgCur').textContent=pages>0?currentPage:'—';
  document.getElementById('pgTotal').textContent=pages>0?pages:'—';
  document.getElementById('headerCount').textContent='共 '+allData.length+' 条记录';
  var tbody=document.getElementById('tblBody');
  if(slice.length===0){tbody.innerHTML='<tr class="empty-row"><td colspan="12">未找到匹配数据</td></tr>';renderPager(pages);return;}
  var rows='';
  for(var i=0;i<slice.length;i++){
    var r=slice[i],n=start+i+1;
    var img=r.img_url||'',asin=r.asin||'',ttl=r.title||'',kw=r.keywords||'';
    var pt=r.product_type||r.pt||'',q=r.question||'',ri=r.ri_comment||'';
    var cm=r.comment||'',cu=r.create_user||'',tt=r.tt_link||'',pq=r.pt_qrd_link||'';
    var imgCell=img?'<div class="c-img"><img src="'+x(img)+'" alt="" onerror="this.parentNode.innerHTML=\'<div class=noimg>N/A</div>\'"></div>':'<div class="c-img"><div class="noimg">N/A</div></div>';
    var ttCell=tt?'<div class="c-link"><a href="'+x(tt)+'" target="_blank">�� 链接</a></div>':'';
    var pqCell=pq?'<div class="c-link"><a href="'+x(pq)+'" target="_blank">�� 链接</a></div>':'';
    rows+='<tr onclick="selRow(this)">'
      +'<td class="rn">'+n+'</td>'
      +'<td>'+imgCell+'</td>'
      +'<td class="c-asin" title="'+x(asin)+'">'+x(asin)+'</td>'
      +'<td title="'+x(ttl)+'">'+x(ttl)+'</td>'
      +'<td class="c-kw" title="'+x(kw)+'">'+x(kw)+'</td>'
      +'<td>'+(pt?'<span class="c-pt">'+x(pt)+'</span>':'')+'</td>'
      +'<td title="'+x(q)+'">'+x(q)+'</td>'
      +'<td title="'+x(ri)+'">'+x(ri)+'</td>'
      +'<td title="'+x(cm)+'">'+x(cm)+'</td>'
      +'<td title="'+x(cu)+'">'+x(cu)+'</td>'
      +'<td>'+ttCell+'</td>'
      +'<td>'+pqCell+'</td>'
      +'</tr>';
  }
  tbody.innerHTML=rows;
  renderPager(pages);
}
function selRow(tr){
  document.querySelectorAll('#tblBody tr.selected').forEach(function(r){r.classList.remove('selected');});
  tr.classList.add('selected');
}
function renderPager(pages){
  var pg=document.getElementById('pager');
  if(pages<=1){pg.innerHTML='';return;}
  var h='<button class="pg-btn" onclick="goPage('+(currentPage-1)+')" '+(currentPage===1?'disabled':'')+'>‹ 上一页</button>';
  var range=[];
  for(var i=1;i<=pages;i++){
    if(i===1||i===pages||(i>=currentPage-2&&i<=currentPage+2)) range.push(i);
    else if(range[range.length-1]!=='…') range.push('…');
  }
  range.forEach(function(p){
    if(p==='…') h+='<span style="padding:0 5px;color:#b0c4d4;font-weight:700">…</span>';
    else h+='<button class="pg-btn '+(p===currentPage?'active':'')+'" onclick="goPage('+p+')">'+p+'</button>';
  });
  h+='<button class="pg-btn" onclick="goPage('+(currentPage+1)+')" '+(currentPage===pages?'disabled':'')+'>下一页 ›</button>';
  h+='<span class="pg-info">每页 '+PAGE+' 条</span>';
  pg.innerHTML=h;
}
function goPage(p){
  var pages=Math.ceil(filteredData.length/PAGE);
  if(p<1||p>pages) return;
  currentPage=p; render();
  document.getElementById('tblOuter').scrollTop=0;
}
function x(s){return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');}
function showToast(msg){
  var t=document.getElementById('toast');
  t.textContent=msg; t.classList.add('show');
  setTimeout(function(){t.classList.remove('show');},3000);
}
</script>
</body>
</html>
