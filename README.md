



<html lang="en">
<head>
<meta charset="UTF-8">
<title>Moderation Panel</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
* { box-sizing: border-box; }

body {
  margin: 0;
  height: 100vh;
  display: flex;
  background: #0f172a;
  font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", system-ui, sans-serif;
  color: #e2e8f0;
}

/* SIDEBAR */
.sidebar {
  width: 180px;
  background: rgba(15,23,42,0.6);
  backdrop-filter: blur(18px);
  border-right: 1px solid rgba(255,255,255,0.08);
  padding: 20px 14px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.sidebar-title {
  font-size: 18px;
  font-weight: 600;
  margin-bottom: 12px;
  color: #93c5fd;
}

.sidebar button {
  width: 100%;
  padding: 12px;
  border-radius: 12px;
  border: none;
  background: rgba(255,255,255,0.06);
  color: #e2e8f0;
  font-size: 14px;
  text-align: left;
  cursor: pointer;
  transition: 0.2s;
}

.sidebar button:hover,
.sidebar button.active {
  background: rgba(255,255,255,0.14);
}

/* MAIN PANEL */
.main {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}

.panel {
  width: 480px;
  background: rgba(15,23,42,0.75);
  backdrop-filter: blur(22px);
  border-radius: 22px;
  border: 1px solid rgba(255,255,255,0.08);
  padding: 24px;
  box-shadow: 0 20px 40px rgba(0,0,0,0.4);
}

.panel h2 {
  margin: 0 0 14px 0;
  font-size: 20px;
  font-weight: 600;
  color: #bfdbfe;
}

input, textarea {
  width: 100%;
  padding: 12px;
  margin-top: 10px;
  border-radius: 12px;
  border: 1px solid rgba(255,255,255,0.1);
  background: rgba(255,255,255,0.05);
  color: #e2e8f0;
  outline: none;
  font-size: 14px;
}

textarea {
  height: 90px;
  resize: none;
}

button.send-btn {
  width: 100%;
  margin-top: 16px;
  padding: 12px;
  border-radius: 12px;
  border: none;
  background: #3b82f6;
  color: white;
  font-size: 15px;
  cursor: pointer;
  transition: 0.2s;
}

button.send-btn:hover {
  background: #60a5fa;
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

<!-- MAIN PANEL -->
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

function showForm(type) {
  document.querySelectorAll(".sidebar button").forEach(b => b.classList.remove("active"));
  document.getElementById("btn-" + type).classList.add("active");

  let html = "";

  if (type === "part") {
    html = `
      <h2>Partnership</h2>
      <input id="server" placeholder="Server Name">
      <input id="owner" placeholder="Server Owner">
      <input id="logo" placeholder="Logo URL">
      <button class="send-btn" onclick="sendPart()">Send</button>
    `;
  }

  if (type === "blacklist") {
    html = `
      <h2>Blacklist User</h2>
      <input id="user" placeholder="Username">
      <input id="id" placeholder="Discord ID">
      <textarea id="reason" placeholder="Reason"></textarea>
      <button class="send-btn" onclick="sendBlacklist()">Send</button>
    `;
  }

  if (type === "warn") {
    html = `
      <h2>Warn User</h2>
      <input id="id" placeholder="Discord ID">
      <textarea id="reason" placeholder="Reason"></textarea>
      <button class="send-btn" onclick="sendWarn()">Send</button>
    `;
  }

  if (type === "kick") {
    html = `
      <h2>Kick User</h2>
      <input id="id" placeholder="Discord ID">
      <textarea id="reason" placeholder="Reason"></textarea>
      <button class="send-btn" onclick="sendKick()">Send</button>
    `;
  }

  if (type === "ban") {
    html = `
      <h2>Ban User</h2>
      <input id="id" placeholder="Discord ID">
      <textarea id="reason" placeholder="Reason"></textarea>
      <button class="send-btn" onclick="sendBan()">Send</button>
    `;
  }

  if (type === "unblacklist") {
    html = `
      <h2>Unblacklist User</h2>
      <input id="id" placeholder="Discord ID">
      <button class="send-btn" onclick="sendUnblacklist()">Send</button>
    `;
  }

  if (type === "msg") {
    html = `
      <h2>Send Message</h2>
      <textarea id="msg" placeholder="Message"></textarea>
      <button class="send-btn" onclick="sendMsg()">Send</button>
    `;
  }

  if (type === "say") {
    html = `
      <h2>Send Embed Message</h2>
      <textarea id="msg" placeholder="Message"></textarea>
      <button class="send-btn" onclick="sendSay()">Send</button>
    `;
  }

  if (type === "embed") {
    html = `
      <h2>Custom Embed</h2>
      <input id="title" placeholder="Title">
      <textarea id="desc" placeholder="Description"></textarea>
      <input id="color" placeholder="Color (hex)">
      <input id="thumb" placeholder="Thumbnail URL (optional)">
      <button class="send-btn" onclick="sendEmbed()">Send</button>
    `;
  }

  if (type === "ping") {
    html = `
      <h2>Ping</h2>
      <button class="send-btn" onclick="sendPing()">Send Ping</button>
    `;
  }

  document.getElementById("panel").innerHTML = html;
}

/* SEND FUNCTIONS */
function sendWebhook(url, payload) {
  fetch(url, {
    method: "POST",
    headers: {"Content-Type": "application/json"},
    body: JSON.stringify(payload)
  });
}

/* PARTNERSHIP */
function sendPart() {
  sendWebhook(WH_PARTNER, {
    username: "Partnership",
    embeds: [{
      title: "New Partnership",
      color: 0x3b82f6,
      thumbnail: { url: document.getElementById("logo").value },
      fields: [
        { name: "Server", value: document.getElementById("server").value },
        { name: "Owner", value: document.getElementById("owner").value }
      ]
    }]
  });
}

/* BLACKLIST */
function sendBlacklist() {
  sendWebhook(WH_PUNISH, {
    username: "Blacklist",
    embeds: [{
      title: "User Blacklisted",
      color: 0x000000,
      fields: [
        { name: "Username", value: document.getElementById("user").value },
        { name: "ID", value: document.getElementById("id").value },
        { name: "Reason", value: document.getElementById("reason").value }
      ]
    }]
  });
}

/* WARN */
function sendWarn() {
  sendWebhook(WH_PUNISH, {
    username: "Warn",
    embeds: [{
      title: "User Warned",
      color: 0xfacc15,
      fields: [
        { name: "ID", value: document.getElementById("id").value },
        { name: "Reason", value: document.getElementById("reason").value }
      ]
    }]
  });
}

/* KICK */
function sendKick() {
  sendWebhook(WH_PUNISH, {
    username: "Kick",
    embeds: [{
      title: "User Kicked",
      color: 0xf97316,
      fields: [
        { name: "ID", value: document.getElementById("id").value },
        { name: "Reason", value: document.getElementById("reason").value }
      ]
    }]
  });
}

/* BAN */
function sendBan() {
  sendWebhook(WH_PUNISH, {
    username: "Ban",
    embeds: [{
      title: "User Banned",
      color: 0xef4444,
      fields: [
        { name: "ID", value: document.getElementById("id").value },
        { name: "Reason", value: document.getElementById("reason").value }
      ]
    }]
  });
}

/* UNBLACKLIST */
function sendUnblacklist() {
  sendWebhook(WH_PUNISH, {
    username: "Unblacklist",
    embeds: [{
      title: "User Unblacklisted",
      color: 0x22c55e,
      fields: [
        { name: "ID", value: document.getElementById("id").value }
      ]
    }]
  });
}

/* MESSAGE */
function sendMsg() {
  sendWebhook(WH_MSG, {
    username: "Message",
    content: document.getElementById("msg").value
  });
}

/* SAY */
function sendSay() {
  sendWebhook(WH_MSG, {
    username: "Say",
    embeds: [{
      description: document.getElementById("msg").value,
      color: 0x3b82f6
    }]
  });
}

/* EMBED */
function sendEmbed() {
  sendWebhook(WH_MSG, {
    username: "Embed",
    embeds: [{
      title: document.getElementById("title").value,
      description: document.getElementById("desc").value,
      color: parseInt(document.getElementById("color").value.replace("#",""), 16),
      thumbnail: { url: document.getElementById("thumb").value }
    }]
  });
}

/* PING */
function sendPing() {
  sendWebhook(WH_MSG, { content: "Ping!" });
}
</script>

</body>
</html>
