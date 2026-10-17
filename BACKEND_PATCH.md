# 若你另外修改過 Code.gs，僅更換個人資料讀取函式

下面是 v4 新版完整的 `raMine_(p)` 函式。只需在原 Apps Script 中找到 `function raMine_(p){`，將整個函式替換成下列內容；其他函式、工作表與參數保持原樣。

此函式沿用前一版 `raPerson_`、`raRows_`、`raCaseData_`、`RA_CASES`，不需要增加新的欄位。

```javascript
function raMine_(p){
  const person=raPerson_(p.code);
  const id=person.id;
  const questionRows=raRows_('questions');
  const replyRows=raRows_('replies');
  const myQuestions=questionRows.filter(r=>String(r[2])===id&&String(r[4])==='visible');
  const myReplies=replyRows.filter(r=>String(r[3])===id&&String(r[5])==='visible');
  const questionsById={};
  questionRows.filter(r=>String(r[4])==='visible').forEach(r=>{questionsById[String(r[1])]=String(r[3]||'')});
  return {ok:true,profile:{name:person.name,anonymous:person.anonymous},intro:raRows_('intro').filter(r=>String(r[1])===id).map(r=>({topic:r[2],content:r[3]}))[0]||null,
    process:raRows_('process').filter(r=>String(r[1])===id).map(r=>({stage:String(r[2]),problem:r[3],resources:r[4]})),
    cases:raRows_('cases').filter(r=>String(r[1])===id&&RA_CASES.includes(String(r[2]))).map(raCaseData_),
    questions:myQuestions.map(r=>String(r[1])),
    replies:myReplies.map(r=>String(r[1])),
    // v4: only this participant's currently visible Q&A contents for personal PDF export.
    questionEntries:myQuestions.map(r=>({questionId:String(r[1]),at:String(r[0]),content:String(r[3]||'')})),
    replyEntries:myReplies.map(r=>({replyId:String(r[1]),questionId:String(r[2]),at:String(r[0]),content:String(r[4]||''),questionText:questionsById[String(r[2])]||'原提問已撤回'}))};
}
```

完成後請將 Apps Script **現有部署作業更新為新版本**，而不是只按儲存。再更新 GitHub `index.html`。
