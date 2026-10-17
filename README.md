<!DOCTYPE html><html><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>MusicBot</title>
<style>
body{margin:0;font-family:sans-serif;background:#0e1621;color:#fff;display:flex;flex-direction:column;height:100vh}
#player{background:#000;flex:none}
#chat{flex:1;overflow-y:auto;padding:10px}
.m{max-width:85%;margin:6px 0;padding:8px 12px;border-radius:12px;background:#182533;word-wrap:break-word}
.me{margin-left:auto;background:#2b5278}
.r{display:flex;gap:8px;align-items:center;padding:6px;margin-top:6px;background:#0e1621;border-radius:8px;cursor:pointer}
.r img{width:80px;border-radius:6px}
#bar{display:flex;gap:6px;padding:8px;background:#17212b}
#bar input{flex:1;padding:10px;border-radius:20px;border:0;font-size:16px}
#bar button{border:0;border-radius:20px;padding:0 16px;background:#2b5278;color:#fff;font-size:16px}
</style></head><body>
<div id="player"></div>
<div id="chat"></div>
<div id="bar"><input id="t" placeholder="Canción o artista..."><button onclick="go()">➤</button></div>
<script src="https://www.youtube.com/iframe_api"></script>
<script>
const APIS=["https://pipedapi.kavin.rocks","https://pipedapi.leptons.xyz","https://pipedapi.nosebs.ru","https://pipedapi-libre.kavin.rocks","https://pipedapi.adminforge.de","https://api.piped.yt","https://pipedapi.drgns.space","https://pipedapi.owo.si","https://pipedapi.ducks.party","https://api.piped.private.coffee"];
let p,good=0;
function onYouTubeIframeAPIReady(){p=new YT.Player('player',{height:200,width:'100%',events:{onError:()=>say('Ese video no permite reproducirse aquí 😕 Prueba otro.')}})}
const chat=document.getElementById('chat'),t=document.getElementById('t');
t.addEventListener('keydown',e=>{if(e.key==='Enter')go()});
function say(txt,me){const d=document.createElement('div');d.className='m'+(me?' me':'');d.textContent=txt;chat.appendChild(d);chat.scrollTop=chat.scrollHeight;return d}
say('¡Hola! 🎵 Escribe una canción o artista y te la pongo.');
async function buscar(q){
 for(let n=0;n<APIS.length;n++){
  const i=(good+n)%APIS.length;
  try{
   const c=new AbortController();const to=setTimeout(()=>c.abort(),6000);
   const r=await fetch(APIS[i]+'/search?q='+encodeURIComponent(q)+'&filter=videos',{signal:c.signal});
   clearTimeout(to);
   if(!r.ok)continue;
   const j=await r.json();
   if(j.items){good=i;return j.items}
  }catch(e){}
 }
 throw new Error('Los servidores están saturados, intenta de nuevo en un momento');
}
async function go(){
 const q=t.value.trim();if(!q)return;t.value='';say(q,true);
 const b=say('Buscando...');
 try{
  const items=(await buscar(q)).filter(x=>x.url&&x.url.includes('v=')).slice(0,5);
  if(!items.length){b.textContent='No encontré nada 😕';return}
  b.textContent='Toca la que quieres:';
  items.forEach(x=>{
   const id=x.url.split('v=')[1].split('&')[0];
   const d=document.createElement('div');d.className='r';
   const im=document.createElement('img');im.src='https://i.ytimg.com/vi/'+id+'/mqdefault.jpg';
   const s=document.createElement('span');s.textContent=x.title;
   d.append(im,s);
   d.onclick=()=>{p.loadVideoById(id);say('▶ '+x.title)};
   b.appendChild(d);
  });
  chat.scrollTop=chat.scrollHeight;
 }catch(e){b.textContent=e.message}
}
</script></body></html>
