



<html lang="en">
<head>
<meta charset="UTF-8">
<title>Glassy Command Console</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
* { box-sizing: border-box; }

body {
  margin: 0;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: radial-gradient(circle at top, #1f2933 0, #020617 55%);
  font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", system-ui, sans-serif;
  color: #e5e7eb;
}

/* GLASS PANEL */
.panel {
  width: 520px;
  max-width: 92vw;
  background: rgba(15,23,42,0.78);
  border-radius: 24px;
  border: 1px solid rgba(148,163,184,0.35);
  box-shadow:
    0 18px 45px rgba(0,0,0,0.65),
    0 0 0 1px rgba(15,23,42,0.9);
  backdrop-filter: blur(22px);
  padding: 18px 18px 14px;
  display: flex;
  flex-direction: column;
}

/* HEADER */
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 10px;
}

.badge {
  width: 30px;
  height: 30px;
  border-radius: 999px;
  background: radial-gradient(circle at 30% 20%, #60a5fa, #1d4ed8);
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: 700;
  font-size: 15px;
  box-shadow: 0 6px 16px rgba(37,99,235,0.7);
}

.title-block {
  display: flex;
  flex-direction: column;
}

.title {
  font-size: 16px;
  font-weight: 600;
  letter-spacing: 0.01em;
}

.subtitle {
  font-size: 12px;
  color: #9ca3af;
}

/* STATUS PILL */
.status-pill {
  padding: 4px 10px;
  border-radius: 999px;
  background: rgba(15,118,110,0.18);
  border: 1px solid rgba(45,212,191,0.4);
  font-size: 11px;
  color: #a5f3fc;
}

/* OUTPUT */
.output {
  margin-top: 8px;
  flex: 1;
  min-height: 210px;
  max-height: 260px;
  background: radial-gradient(circle at top left, rgba(30,64,175,0.18), rgba(15,23,42,0.9));
  border-radius: 18px;
  border: 1px solid rgba(31,41,55,0.9);
  padding: 10px 11px;
  font-size: 13px;
  overflow-y: auto;
}

/* INPUT ROW */
.input-row {
  margin-top: 10px;
  display: flex;
  gap: 8px;
}

input {
  flex: 1;
  padding: 11px 12px;
  border-radius: 999px;
  border: 1px solid rgba(55,65,81,0.9);
  background: rgba(15,23,42,0.9);
  color: #e5e7eb;
  outline: none;
  font-size: 14px;
  transition: border-color .18s ease, box-shadow .18s ease, background .18s ease;
}

input::placeholder {
  color: #6b7280;
}

input:focus {
  border-color: #3b82f6;
  box-shadow: 0 0 0 1px rgba(59,130,246,0.7);
  background: rgba(15,23,42,0.96);
}

button {
  padding: 0 16px;
  border-radius: 999px;
  border: none;
  background: linear-gradient(135deg, #3b82f6, #2563eb);
  color: white;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  box-shadow: 0 8px 18px rgba(37,99,235,0.55);
  transition: transform .12s ease, box-shadow .12s ease, filter .12s ease;
}

button:hover {
  transform: translateY(-1px);
  box-shadow: 0 10px 22px rgba(37,99,235,0.7);
  filter: brightness(1.03);
}

button:active {
  transform: translateY(0);
  box-shadow: 0 6px 14px rgba(37,99,235,0.55);
}

/* LOG LINES */
.log-line {
  margin-bottom: 5px;
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

<div class="panel">
  <div class="header">
    <div class="header-left">
      <div class="badge">M</div>
      <div class="title-block">
        <div class="title">Moderation Console</div>
        <div class="subtitle">Type <strong>!help</strong> to view all commands</div>
      </div>
    </div>
    <div class="status-pill">Connected • Local</div>
  </div>

  <div class="output" id="output"></div>

  <div class="input-row">
    <input id="cmd" placeholder='Example: !msg "Hello world"'>
    <button onclick="runCommand()">Run</button>
  </div>
</div>

<script>
/* WEBHOOKS (EXACT STRINGS YOU GAVE) */
const WEBHOOK_PUNISH  = "https://discord.com/api/webhooks/1487259518747279551/Fd4NWmGoJY-dLP9k3YpaBwYVljvhNllM95nQXk_Efoq5uaE-AIfX6WRmoz5NjcHq2-v";
const WEBHOOK_MSG     = "https://discord.com/api/webhooks/1487260095828988096/WDRCzlERI7F98xj9qtnimt4zRXvhNllM95nQX88863GSltJtcMp64sdzeobx4auGb2I7";
const WEBHOOK_PARTNER = "https://discord.com/api/webhooks/1487256678507479112/fxsKoLZKQKndL9t5NLoNLQarkZvA6iaGhxBsI7_SBw8tdQAOq5Piw79xMUsExay9uqLK";

const cmdInput = document.getElementById("cmd");
const output   = document.getElementById("output");

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

  // Message / utility
  if (raw.startsWith("!msg ")) return handleMsg(raw);
  if (raw.startsWith("!say ")) return handleSay(raw);
  if (raw === "!embed") return handleEmbed();
  if (raw === "!ping") return handlePing();

  // Punishments
  if (raw.startsWith("!warn ")) return handleWarn(raw);
  if (raw.startsWith("!kick ")) return handleKick(raw);
  if (raw.startsWith("!ban "))  return handleBan(raw);
  if (raw === "!blacklist")     return handleBlacklist();
  if (raw.startsWith("!unblacklist ")) return handleUnblacklist(raw);

  // Partnership
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

  const embed = { title, description, color };
  if (thumb.trim()) embed.thumbnail = { url: thumb.trim() };

  const payload = { username: "Console Embed", embeds: [embed] };
  sendWebhook(WEBHOOK_MSG, payload, "Custom embed sent.", "Failed to send custom embed.");
}

function handlePing() {
  const start = performance.now();
  const payload = { content: "Ping..." };
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

/* INITIAL HINT */
log("Type !help to see all commands.");
</script>

</body>
</html>
