<script src="/assets/js/ajaxRequest.js"></script>
If you are a BAS provider, you can use this page to see how your contributions are seen through the eyes of the BAS-harvester.

Select a provider: <select id="providerSelect" onchange="harvest(this.options[this.selectedIndex].value)"><option selected disabled>Choose a provider...</option></select>
<div id="harvestStatus"></div>

<script type="text/javascript">
let providers = {};
getProviders = ajaxPromise({ type:"GET", url:"/providers.json", responseType:'json' })
.then((xhr)=>{
  providers = xhr.response;
  select = document.getElementById('providerSelect');
  for (p in providers) {
    option = document.createElement('option');
    option.value = p;
    optionText = document.createTextNode(p+' - '+providers[p].name);
    option.appendChild(optionText);
    select.appendChild(option);
  }
})

function harvest(providerId) {
  const provider = providers[providerId];
  let atlases;
  if (!atlases) {
    return ajaxPromise({ type:"GET", url:provider.basUrl, responseType:'text' })
    .then((xhr)=>{
      console.log('Atlases downloaded.');
      console.log(xhr);
    },(xhr) => {
      console.log('ERROR');
    });
  }
}
</script>
