



<html lang="en">
<head>
<meta charset="UTF-8">
<title>Moderation Panel</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
*{box-sizing:border-box;margin:0;padding:0;font-family:-apple-system,BlinkMacSystemFont,"SF Pro Text",system-ui,sans-serif;}
body{
  height:100vh;
  display:flex;
  background:linear-gradient(135deg,#0f172a,#1e293b);
  color:#e2e8f0;
  overflow:hidden;
}

/* SIDEBAR */
.sidebar{
  width:220px;
  background:rgba(15,23,42,0.55);
  backdrop-filter:blur(22px);
  border-right:1px solid rgba(255,255,255,0.08);
  padding:26px 18px;
  display:flex;
  flex-direction:column;
  gap:12px;
  box-shadow:4px 0 20px rgba(0,0,0,0.35);
}
.sidebar-title{
  font-size:22px;
  font-weight:600;
  margin-bottom:16px;
  color:#93c5fd;
}
.sidebar button{
  width:100%;
  padding:14px 16px;
  border-radius:14px;
  border:none;
  background:rgba(255,255,255,0.06);
  color:#e2e8f0;
  font-size:15px;
  text-align:left;
  cursor:pointer;
  transition:0.25s;
}
.sidebar button:hover,
.sidebar button.active{
  background:rgba(255,255,255,0.18);
  transform:translateX(6px);
}

/* MAIN AREA */
.main{
  flex:1;
  overflow-y:auto;
  padding:40px;
}

/* PANEL */
.panel{
  max-width:620px;
  margin:0 auto 60px auto;
  background:rgba(255,255,255,0.06);
  backdrop-filter:blur(28px);
  border-radius:26px;
  border:1px solid rgba(255,255,255,0.12);
  padding:32px;
  box-shadow:0 25px 60px rgba(0,0,0,0.45);
  animation:fadeIn .4s ease;
}
@keyframes fadeIn{
  from{opacity:0;transform:translateY(10px);}
  to{opacity:1;transform:translateY(0);}
}
.panel h2{
  font-size:24px;
  font-weight:600;
  margin-bottom:18px;
  color:#bfdbfe;
}

input,textarea{
  width:100%;
  padding:14px;
  margin-top:14px;
  border-radius:14px;
  border:1px solid rgba(255,255,255,0.12);
  background:rgba(255,255,255,0.08);
  color:#e2e8f0;
  font-size:15px;
  outline:none;
  transition:0.2s;
}
input:focus,textarea:focus{
  border-color:#60a5fa;
  background:rgba(255,255,255,0.12);
}
textarea{height:110px;resize:none;}

button.send-btn{
  width:100%;
  margin-top:24px;
  padding:15px;
  border-radius:14px;
  border:none;
  background:linear-gradient(135deg,#3b82f6,#2563eb);
  color:white;
  font-size:17px;
  font-weight:600;
  cursor:pointer;
  transition:0.25s;
  box-shadow:0 12px 28px rgba(37,99,235,0.45);
}
button.send-btn:hover{
  transform:translateY(-2px);
  box-shadow:0 16px 34px rgba(37,99,235,0.6);
}
</style>
</head>
<body>

<!-- SIDEBAR -->
<div class="sidebar">
  <div class="sidebar-title">Actions</div>
  <button onclick="showForm('part')" id="btn-part">Partnership</button>
  <button onclick="showForm('blacklist')" id="btn-blacklist">Blacklist</button>
  <button onclick="showForm('warn')" id="btn-warn">Warn</button>
  <button onclick="showForm('kick')" id="btn-kick">Kick</button>
  <button onclick="showForm('ban')" id="btn-ban">Ban</button>
  <button onclick="showForm('unblacklist')" id="btn-unblacklist">Unblacklist</button>
  <button onclick="showForm('msg')" id="btn-msg">Message</button>
  <button onclick="showForm('say')" id="btn-say">Say</button>
  <button onclick="showForm('embed')" id="btn-embed">Embed</button>
  <button onclick="showForm('ping')" id="btn-ping">Ping</button>
</div>

<!-- MAIN -->
<div class="main">
  <div class="panel" id="panel">
    <h2>Select an action</h2>
    <p>Choose an option from the left sidebar.</p>
  </div>
</div>

<script>
/* WEBHOOKS */
const WH_PUNISH  = "https://discord.com/api/webhooks/1487259518747279551/Fd4NWmGoJY-dLP9k3YpaBwYVljvhNllM95nQXk_Efoq5uaE-AIfX6WRmoz5NjcHq2-v";
const WH_MSG     = "https://discord.com/api/webhooks/1487260095828988096/WDRCzlERI7F98xj9qtnimt4zRXvhNllM95nQX88863GSltJtcMp64sdzeobx4auGb2I7";
const WH_PARTNER = "https://discord.com/api/webhooks/1487256678507479112/fxsKoLZKQKndL9t5NLoNLQarkZvA6iaGhxBsI7_SBw8tdQAOq5Piw79xMUsExay9uqLK";

function showForm(type){
  document.querySelectorAll(".sidebar button").forEach(b=>b.classList.remove("active"));
  document.getElementById("btn-"+type).classList.add("active");

  const forms={
    part:`
      <h2>Partnership</h2>
      <input id="server" placeholder="Server Name">
      <input id="owner" placeholder="Server Owner">
      <input id="logo" placeholder="Logo URL">
      <button class="send-btn" onclick="sendPart()">Send</button>
    `,
    blacklist:`
      <h2>Blacklist User</h2>
      <input id="user" placeholder="Username">
      <input id="id" placeholder="Discord ID">
      <textarea id="reason" placeholder="Reason"></textarea>
      <button class="send-btn" onclick="sendBlacklist()">Send</button>
    `,
    warn:`
      <h2>Warn User</h2>
      <input id="id" placeholder="Discord ID">
      <textarea id="reason" placeholder="Reason"></textarea>
      <button class="send-btn" onclick="sendWarn()">Send</button>
    `,
    kick:`
      <h2>Kick User</h2>
      <input id="id" placeholder="Discord ID">
      <textarea id="reason" placeholder="Reason"></textarea>
      <button class="send-btn" onclick="sendKick()">Send</button>
    `,
    ban:`
      <h2>Ban User</h2>
      <input id="id" placeholder="Discord ID">
      <textarea id="reason" placeholder="Reason"></textarea>
      <button class="send-btn" onclick="sendBan()">Send</button>
    `,
    unblacklist:`
      <h2>Unblacklist User</h2>
      <input id="id" placeholder="Discord ID">
      <button class="send-btn" onclick="sendUnblacklist()">Send</button>
    `,
    msg:`
      <h2>Send Message</h2>
      <textarea id="msg" placeholder="Message"></textarea>
      <button class="send-btn" onclick="sendMsg()">Send</button>
    `,
    say:`
      <h2>Send Embed Message</h2>
      <textarea id="msg" placeholder="Message"></textarea>
      <button class="send-btn" onclick="sendSay()">Send</button>
    `,
    embed:`
      <h2>Custom Embed</h2>
      <input id="title" placeholder="Title">
      <textarea id="desc" placeholder="Description"></textarea>
      <input id="color" placeholder="Color (hex)">
      <input id="thumb" placeholder="Thumbnail URL (optional)">
      <button class="send-btn" onclick="sendEmbed()">Send</button>
    `,
    ping:`
      <h2>Ping</h2>
      <button class="send-btn" onclick="sendPing()">Send Ping</button>
    `
  };

  document.getElementById("panel").innerHTML=forms[type];
}

/* SEND HELPERS */
function sendWebhook(url,payload){
  fetch(url,{
    method:"POST",
    headers:{"Content-Type":"application/json"},
    body:JSON.stringify(payload)
  });
}

/* ACTIONS */
function sendPart(){
  sendWebhook(WH_PARTNER,{
    username:"Partnership",
    embeds:[{
      title:"New Partnership",
      color:0x3b82f6,
      thumbnail:{url:document.getElementById("logo").value},
      fields:[
        {name:"Server",value:document.getElementById("server").value},
        {name:"Owner",value:document.getElementById("owner").value}
      ]
    }]
  });
}

function sendBlacklist(){
  sendWebhook(WH_PUNISH,{
    username:"Blacklist",
    embeds:[{
      title:"User Blacklisted",
      color:0x000000,
      fields:[
        {name:"Username",value:document.getElementById("user").value},
        {name:"ID",value:document.getElementById("id").value},
        {name:"Reason",value:document.getElementById("reason").value}
      ]
    }]
  });
}

function sendWarn(){
  sendWebhook(WH_PUNISH,{
    username:"Warn",
    embeds:[{
      title:"User Warned",
      color:0xfacc15,
      fields:[
        {name:"ID",value:document.getElementById("id").value},
        {name:"Reason",value:document.getElementById("reason").value}
      ]
    }]
  });
}

function sendKick(){
  sendWebhook(WH_PUNISH,{
    username:"Kick",
    embeds:[{
      title:"User Kicked",
      color:0xf97316,
      fields:[
        {name:"ID",value:document.getElementById("id").value},
        {name:"Reason",value:document.getElementById("reason").value}
      ]
    }]
  });
}

function sendBan(){
  sendWebhook(WH_PUNISH,{
    username:"Ban",
    embeds:[{
      title:"User Banned",
      color:0xef4444,
      fields:[
        {name:"ID",value:document.getElementById("id").value},
        {name:"Reason",value:document.getElementById("reason").value}
      ]
    }]
  });
}

function sendUnblacklist(){
  sendWebhook(WH_PUNISH,{
    username:"Unblacklist",
    embeds:[{
      title:"User Unblacklisted",
      color:0x22c55e,
      fields:[
        {name:"ID",value:document.getElementById("id").value}
      ]
    }]
  });
}

function sendMsg(){
  sendWebhook(WH_MSG,{
    username:"Message",
    content:document.getElementById("msg").value
  });
}

function sendSay(){
  sendWebhook(WH_MSG,{
    username:"Say",
    embeds:[{
      description:document.getElementById("msg").value,
      color:0x3b82f6
    }]
  });
}

function sendEmbed(){
  sendWebhook(WH_MSG,{
    username:"Embed",
    embeds:[{
      title:document.getElementById("title").value,
      description:document.getElementById("desc").value,
      color:parseInt(document.getElementById("color").value.replace("#",""),16),
      thumbnail:{url:document.getElementById("thumb").value}
    }]
  });
}

function sendPing(){
  sendWebhook(WH_MSG,{content:"Ping!"});
}
</script>

</body>
</html>
