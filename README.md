# Introduction

第一本gitbook


<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
<script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>
<div id="gitalk-container"></div>
var gitalk = new Gitalk({
  "clientID": "43eb1cdbe310d7c13a36",
  "clientSecret": "aebf6144fb6852d049c0e5c866ad3a6708bef20f",
  "repo": "gitbook",
  "owner": "RalstonLiu",
  "admin": ["RalstonLiu"],
  "id": location.pathname,      
  "distractionFreeMode": false  
});
gitalk.render("gitalk-container");
