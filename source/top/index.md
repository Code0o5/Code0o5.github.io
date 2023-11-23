---
title: 热门文章Top10
comments: false
date: 2019-07-04 02:44:43

---

<div id="post-rank">
  <ol>
  </ol>
</div>


<script src="//cdn.jsdelivr.net/npm/leancloud-storage@3.10.0/dist/av-min.js"></script>
<script>
  var APP_ID = "FmrBHAJa1fEUdPOvXyANbPJ5-gzGzoHsz";  //输入个人LeanCloud账号AppID
  var APP_KEY = "Dx4BoLx3FnaC90LK6h8IEgLk";  //输入个人LeanCloud账号AppKey
  AV.init({
    appId: APP_ID,
    appKey: APP_KEY
  });


  var query = new AV.Query('Counter');//表名
  query.descending('time'); //结果按阅读次数降序排序
  query.limit(10);  //最终只返回10条结果
  query.find().then( response => {
    var content = response.reduce( (accum, {attributes}) => {
      accum += `<li><p><div class="prefix">【热度 ${attributes.time} ℃】<a href="${attributes.url}">${attributes.title}</a></div></p></li>`
      return accum;
    },"")
    document.querySelector("#post-rank ol").innerHTML = content;
  })
  .catch( error => {
    console.log(error);
  });
</script>

<style type="text/css">
  #post-rank {
    text-align: left;
  }
  #post-rank .prefix {
    color: #ff4d4f;
    display: inline-block;
  }
</style>
