




<html lang="en">
<head>
<meta charset="UTF-8">
<title>Moderation Command Console</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
* { box-sizing: border-box; }

body {
  margin: 0;
  height: 100vh;
  display: flex;
  font-family: "Inter", system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
  background: #020617;
  color: #e5e7eb;
}

/* SIDEBAR */
.sidebar {
  width: 120px;
  background: #020617;
  border-right: 1px solid rgba(31,41,55,1);
  display: flex;
  flex-direction: column;
  padding: 12px 8px;
}

.sidebar-logo {
  height: 40px;
  margin-bottom: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  color: #60a5fa;
  font-size: 18px;
}

.sidebar-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 10px 4px;
  border-radius: 10px;
  cursor: pointer;
  color: #9ca3af;
  margin-bottom: 6px;
  transition: background .2s, color .2s;
  font-size: 12px;
}

.sidebar-item span.icon {
  font-size: 20px;
  margin-bottom: 4px;
}

.sidebar-item.active,
.sidebar-item:hover {
  background: #111827;
  color: #e5e7eb;
}

/* MAIN PANEL */
.main {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

.panel {
  width: 720px;
  background: rgba(15,23,42,0.9);
  border-radius: 18px;
  border: 1px solid rgba(148,163,184,0.3);
  box-shadow: 0 0 40px rgba(0,0,0,0.7);
  padding: 18px 18px 14px;
  display: flex;
  flex-direction: column;
}

/* HEADER */
.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.panel-title {
  font-size: 18px;
  font-weight: 600;
  color: #bfdbfe;
}

.panel-sub {
  font-size: 12px;
  color: #9ca3af;
}

/* OUTPUT */
.output {
  flex: 1;
  background: #020617;
  border-radius: 12px;
  border: 1px solid rgba(31,41,55,1);
  padding: 10px;
  font-size: 13px;
  overflow-y: auto;
  margin-bottom: 10px;
}

/* INPUT ROW */
.input-row {
  display: flex;
  gap: 8px;
}

input {
  flex: 1;
  padding: 10px;
  border-radius: 10px;
  border: 1px solid rgba(55,65,81,1);
  background: #020617;
  color: #e5e7eb;
  outline: none;
  font-size: 14px;
}

input::placeholder {
  color: #6b7280;
}

input:focus {
  border-color: #3b82f6;
  box-shadow: 0 0 0 1px #3b82f6;
}

button {
  padding: 0 16px;
  border-radius: 10px;
  border: none;
  background: #3b82f6;
  color: white;
  font-size: 14px;
  cursor: pointer;
}

button:hover {
  background: #60a5fa;
}

/* LOG LINES */
.log-line {
  margin-bottom: 6px;
  line-height: 1.3;
}

.log-time {
  color: #6b7280;
  font-size: 11px;
  margin-right: 6px;
}

.log-text {
  color: #e5e7eb;
}
</style>
</head>
<body>

<div class="sidebar">
  <div class="sidebar-logo">MOD</div>
  <div class="sidebar-item active">
    <span class="icon">🏠</span>
    <span>Home</span>
  </div>
  <div class="sidebar-item">
    <span class="icon">💻</span>
    <span>Commands</span>
  </div>
  <div class="sidebar-item">
    <span class="icon">📜</span>
    <span>Logs</span>
  </div>
  <div class="sidebar-item">
    <span class="icon">⚙</span>
    <span>Settings</span>
  </div>
</div>

<div class="main">
  <div class="panel">
    <div class="panel-header">
      <div>
        <div class="panel-title">Command Console</div>
        <div class="panel-sub">Type <strong>!help</strong> to see all commands</div>
      </div>
    </div>

    <div class="output" id="output"></div>

    <div class="input-row">
      <input id="cmd" placeholder='Example: !msg "Hello world"'>
      <button onclick="runCommand()">Run</button>
    </div>
  </div>
</div>

<script>
/* WEBHOOKS */
const WEBHOOK_PUNISH = "https://discord.com/api/webhooks/1487259518747279551/Fd4NWmGoJY-dLP9k3YpaBwYVljvhNllM95nQXk_Efoq5uaE-AIfX6WRmoz5NjcHq2-v".replace("k_","k_"); // keep as given
const WEBHOOK_MSG = "https://discord.com/api/webhooks/1487260095828988096/WDRCzlERI7F98xj9qtnimt4zRXvhNllM95nQX88863GSltJtcMp64sdzeobx4auGb2I7";
const WEBHOOK_PARTNER = "https://discord.com/api/webhooks/1487256678507479112/fxsKoLZKQKndL9t5NLoNLQarkZvA6iaGhxBsI7_SBw8tdQAOq5Piw79xMUsExay9uqLK";

const cmdInput = document.getElementById("cmd");
const output = document.getElementById("output");

/* ENTER KEY */
cmdInput.addEventListener("keydown", e => {
  if (e.key === "Enter") runCommand();
});

/* LOGGING */
function log(msg) {
  const line = document.createElement("div");
  line.className = "log-line";

  const t = document.createElement("span");
  t.className = "log-time";
  t.textContent = "[" + new Date().toLocaleTimeString() + "]";

  const text = document.createElement("span");
  text.className = "log-text";
  text.textContent = " " + msg;

  line.appendChild(t);
  line.appendChild(text);
  output.appendChild(line);
  output.scrollTop = output.scrollHeight;
}

/* COMMAND DISPATCH */
function runCommand() {
  const raw = cmdInput.value.trim();
  if (!raw) return;
  log("> " + raw);
  cmdInput.value = "";

  if (raw === "!help") return showHelp();
  if (raw.startsWith("!msg ")) return handleMsg(raw);
  if (raw.startsWith("!say ")) return handleSay(raw);
  if (raw === "!embed") return handleEmbed();
  if (raw === "!ping") return handlePing();

  if (raw.startsWith("!warn ")) return handleWarn(raw);
  if (raw.startsWith("!kick ")) return handleKick(raw);
  if (raw.startsWith("!ban ")) return handleBan(raw);
  if (raw === "!blacklist") return handleBlacklist();
  if (raw.startsWith("!unblacklist ")) return handleUnblacklist(raw);

  if (raw === "!part") return handlePart();

  log("Unknown command. Type !help");
}

/* HELP */
function showHelp() {
  log("Available commands:");
  log('!msg "message"              → Send plain message (message webhook)');
  log('!say "message"              → Send embed message (message webhook)');
  log('!embed                      → Interactive custom embed (message webhook)');
  log('!ping                       → Test latency (message webhook)');
  log('!warn <ID> "reason"         → Warn user (punishment webhook)');
  log('!kick <ID> "reason"         → Kick log (punishment webhook)');
  log('!ban <ID> "reason"          → Ban log (punishment webhook)');
  log('!blacklist                  → Prompt username/ID/reason (punishment webhook)');
  log('!unblacklist <ID>           → Remove from blacklist (punishment webhook)');
  log('!part                       → Partnership embed (partner webhook)');
}

/* MESSAGE COMMANDS */
function handleMsg(raw) {
  const match = raw.match(/^!msg\s+"(.+)"$/);
  if (!match) {
    log('Usage: !msg "your message here"');
    return;
  }
  const message = match[1];
  const payload = { username: "Console", content: message };
  sendWebhook(WEBHOOK_MSG, payload, "Message sent.", "Failed to send message.");
}

function handleSay(raw) {
  const match = raw.match(/^!say\s+"(.+)"$/);
  if (!match) {
    log('Usage: !say "your message here"');
    return;
  }
  const message = match[1];
  const embed = {
    description: message,
    color: 0x3b82f6
  };
  const payload = { username: "Console", embeds: [embed] };
  sendWebhook(WEBHOOK_MSG, payload, "Embed message sent.", "Failed to send embed message.");
}

function handleEmbed() {
  const title = prompt("Embed title:");
  if (title === null) return log("Embed cancelled.");
  const description = prompt("Embed description:");
  if (description === null) return log("Embed cancelled.");
  const colorInput = prompt("Embed color (hex, e.g. #3b82f6):", "#3b82f6");
  if (colorInput === null) return log("Embed cancelled.");
  const thumb = prompt("Thumbnail URL (optional):", "");
  if (thumb === null) return log("Embed cancelled.");

  let color = 0x3b82f6;
  if (/^#?[0-9a-fA-F]{6}$/.test(colorInput)) {
    color = parseInt(colorInput.replace("#",""), 16);
  }

  const embed = {
    title,
    description,
    color
  };
  if (thumb.trim()) {
    embed.thumbnail = { url: thumb.trim() };
  }

  const payload = { username: "Console Embed", embeds: [embed] };
  sendWebhook(WEBHOOK_MSG, payload, "Custom embed sent.", "Failed to send custom embed.");
}

function handlePing() {
  const start = performance.now();
  const payload = { content: "Ping test..." };
  sendWebhook(WEBHOOK_MSG, payload, null, "Ping failed.", () => {
    const ms = Math.round(performance.now() - start);
    log("Pong! Latency: " + ms + "ms");
  });
}

/* PUNISHMENT COMMANDS */
function parsePunish(raw, cmd) {
  const re = new RegExp("^!" + cmd + "\\s+(\\d{17,20})\\s+\"(.+)\"$");
  const match = raw.match(re);
  if (!match) return null;
  return { id: match[1], reason: match[2] };
}

function handleWarn(raw) {
  const data = parsePunish(raw, "warn");
  if (!data) return log('Usage: !warn <ID> "reason"');
  const embed = {
    title: "User Warned",
    color: 0xfacc15,
    fields: [
      { name: "User ID", value: data.id },
      { name: "Reason", value: data.reason }
    ]
  };
  const payload = { username: "Punishments", embeds: [embed] };
  sendWebhook(WEBHOOK_PUNISH, payload, "Warn logged.", "Failed to log warn.");
}

function handleKick(raw) {
  const data = parsePunish(raw, "kick");
  if (!data) return log('Usage: !kick <ID> "reason"');
  const embed = {
    title: "User Kicked",
    color: 0xf97316,
    fields: [
      { name: "User ID", value: data.id },
      { name: "Reason", value: data.reason }
    ]
  };
  const payload = { username: "Punishments", embeds: [embed] };
  sendWebhook(WEBHOOK_PUNISH, payload, "Kick logged.", "Failed to log kick.");
}

function handleBan(raw) {
  const data = parsePunish(raw, "ban");
  if (!data) return log('Usage: !ban <ID> "reason"');
  const embed = {
    title: "User Banned",
    color: 0xef4444,
    fields: [
      { name: "User ID", value: data.id },
      { name: "Reason", value: data.reason }
    ]
  };
  const payload = { username: "Punishments", embeds: [embed] };
  sendWebhook(WEBHOOK_PUNISH, payload, "Ban logged.", "Failed to log ban.");
}

function handleBlacklist() {
  const username = prompt("Username:");
  if (!username) return log("Blacklist cancelled.");
  const id = prompt("Discord ID:");
  if (!id) return log("Blacklist cancelled.");
  const reason = prompt("Reason:");
  if (!reason) return log("Blacklist cancelled.");

  const embed = {
    title: "User Blacklisted",
    color: 0x000000,
    fields: [
      { name: "Username", value: username },
      { name: "Discord ID", value: id },
      { name: "Reason", value: reason }
    ]
  };
  const payload = { username: "Punishments", embeds: [embed] };
  sendWebhook(WEBHOOK_PUNISH, payload, "Blacklist logged.", "Failed to log blacklist.");
}

function handleUnblacklist(raw) {
  const match = raw.match(/^!unblacklist\s+(\d{17,20})$/);
  if (!match) return log("Usage: !unblacklist <ID>");
  const id = match[1];

  const embed = {
    title: "User Unblacklisted",
    color: 0x22c55e,
    fields: [
      { name: "Discord ID", value: id }
    ]
  };
  const payload = { username: "Punishments", embeds: [embed] };
  sendWebhook(WEBHOOK_PUNISH, payload, "Unblacklist logged.", "Failed to log unblacklist.");
}

/* PARTNERSHIP COMMAND */
function handlePart() {
  const serverName = prompt("Server name:");
  if (!serverName) return log("Partnership cancelled.");
  const owner = prompt("Server owner:");
  if (!owner) return log("Partnership cancelled.");
  const logo = prompt("Logo URL:");
  if (!logo) return log("Partnership cancelled.");

  const embed = {
    title: "New Partnership",
    color: 0x3b82f6,
    thumbnail: { url: logo },
    fields: [
      { name: "Server Name", value: serverName },
      { name: "Server Owner", value: owner }
    ]
  };
  const payload = { username: "Partnerships", embeds: [embed] };
  sendWebhook(WEBHOOK_PARTNER, payload, "Partnership sent.", "Failed to send partnership.");
}

/* WEBHOOK SENDER */
function sendWebhook(url, payload, successMsg, errorMsg, onSuccess) {
  log("Sending...");
  fetch(url, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(payload)
  })
  .then(res => {
    if (!res.ok) throw new Error();
    if (successMsg) log(successMsg);
    if (onSuccess) onSuccess();
  })
  .catch(() => {
    if (errorMsg) log(errorMsg);
  });
}

/* INITIAL HELP HINT */
log("Type !help to see all commands.");
</script>

</body>
</html>
