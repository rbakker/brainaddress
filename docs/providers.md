---
layout: default
title: "List of providers"
---

<script type="text/javascript" src="/assets/js/ajaxRequest.js"></script>

# List of providers
<div id="response">Retrieving list of providers...</div>
<script>
function sanitize(s) {
  return s;
}
ajaxPromise({ type:"GET", url:"/providers.json", responseType:'json' })
.then((xhr) => {
  const providers = xhr.response;
  const html = ['<th>Abbreviation</th><th>Name</th><th>Assets</th>'];
  for (let acr in providers) {
    html.push('<td>'+acr+'</td><td><a href="'+sanitize(providers[acr].url)+'">'+sanitize(providers[acr].name)+'</a></td><td><a href="'+sanitize(providers[acr].basUrl)+'">BAS-files</a>');
  }
  document.getElementById('response').innerHTML = '<table><tr>'+html.join('</tr><tr>')+'</tr></table>';
});
</script>
