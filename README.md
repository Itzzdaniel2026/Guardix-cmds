


<html lang="en">
<head>
<meta charset="UTF-8">
<title>Command Box</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
* { box-sizing: border-box; }
body {
  margin: 0;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #020617;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Inter", sans-serif;
  color: #e5e7eb;
}
.box {
  width: 480px;
  background: #020617;
  border-radius: 16px;
  border: 1px solid rgba(148,163,184,0.4);
  box-shadow: 0 0 40px rgba(15,23,42,0.9);
  padding: 18px 18px 14px;
}
.title {
  font-size: 15px;
  color: #9ca3af;
  margin-bottom: 8px;
}
.output {
  height: 220px;
  background: #020617;
  border-radius: 10px;
  border: 1px solid rgba(31,41,55,1);
  padding: 10px;
  font-size: 13px;
  overflow-y: auto;
  margin-bottom: 10px;
}
.input-row {
  display: flex;
  gap: 8px;
}
input {
  flex: 1;
  padding: 10px;
  border-radius: 8px;
  border: 1px solid rgba(55,65,81,1);
  background: #020617;
  color: #e5e7eb;
  outline: none;
  font-size: 14px;
}
input:focus {
  border-color: #3b82f6;
  box-shadow: 0 0 0 1px #3b82f6;
}
button {
  padding: 0 14px;
  border-radius: 8px;
  border: none;
  background: #3b82f6;
  color: white;
  font-size: 14px;
  cursor: pointer;
}
button:hover {
  background: #60a5fa;
}
.log-line {
  margin-bottom: 4px;
}
.log-time {
  color: #6b7280;
  font-size: 11px;
  margin-right: 4px;
}
.log-text {
  color: #e5e7eb;
}
</style>
</head>
<body>

<div class="box">
  <div class="title">Command box</div>
  <div class="output" id="output"></div>
  <div class="input-row">
    <input id="cmd" placeholder='Commands: !msg "message", !blacklist, !part'>
    <button onclick="runCommand()">Run</button>
  </div>
</div>

<script>
const WEBHOOK_MAIN = "https://discord.com/api/webhooks/1487256600312942792/-F7e1ugBmFZWTJ6k6CFpX-Xx0lDHJbpS4qxk1B6RpXVyBLQFFRHfE2lcCT-KA5koD3LJ";
const WEBHOOK_PARTNER = "https://discord.com/api/webhooks/1487256678507479112/fxsKoLZKQKndL9t5NLoNLQarkZvA6iaGhxBsI7_SBw8tdQAOq5Piw79xMUsExay9uqLK";

const cmdInput = document.getElementById("cmd");
const output = document.getElementById("output");

cmdInput.addEventListener("keydown", e => {
  if (e.key === "Enter") runCommand();
});

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

function runCommand() {
  const raw = cmdInput.value.trim();
  if (!raw) return;
  log("> " + raw);
  cmdInput.value = "";

  if (raw.startsWith("!msg ")) {
    handleMsg(raw);
  } else if (raw === "!blacklist") {
    handleBlacklist();
  } else if (raw === "!part") {
    handlePart();
  } else {
    log("Unknown command.");
  }
}

function handleMsg(raw) {
  const match = raw.match(/^!msg\s+"(.+)"$/);
  if (!match) {
    log('Usage: !msg "your message here"');
    return;
  }
  const message = match[1];

  const payload = {
    username: "Command Box",
    content: message
  };

  sendWebhook(WEBHOOK_MAIN, payload, "Message sent.", "Failed to send message.");
}

function handleBlacklist() {
  const username = prompt("Enter username:");
  if (!username) return log("Blacklist cancelled (no username).");

  const id = prompt("Enter Discord ID:");
  if (!id) return log("Blacklist cancelled (no ID).");

  const reason = prompt("Enter reason:");
  if (!reason) return log("Blacklist cancelled (no reason).");

  const embed = {
    title: "New Blacklist Entry",
    color: 0x000000,
    fields: [
      { name: "Username", value: username, inline: false },
      { name: "Discord ID", value: id, inline: false },
      { name: "Reason", value: reason, inline: false }
    ]
  };

  const payload = {
    username: "Blacklist",
    embeds: [embed]
  };

  sendWebhook(WEBHOOK_MAIN, payload, "Blacklist sent.", "Failed to send blacklist.");
}

function handlePart() {
  const serverName = prompt("Server name:");
  if (!serverName) return log("Partner cancelled (no server name).");

  const owner = prompt("Server owner:");
  if (!owner) return log("Partner cancelled (no owner).");

  const logo = prompt("Logo URL (image):");
  if (!logo) return log("Partner cancelled (no logo).");

  const embed = {
    title: "New Partnership",
    color: 0x3b82f6,
    thumbnail: { url: logo },
    fields: [
      { name: "Server Name", value: serverName, inline: false },
      { name: "Server Owner", value: owner, inline: false }
    ]
  };

  const payload = {
    username: "Partnerships",
    embeds: [embed]
  };

  sendWebhook(WEBHOOK_PARTNER, payload, "Partnership sent.", "Failed to send partnership.");
}

function sendWebhook(url, payload, successMsg, errorMsg) {
  log("Sending...");
  fetch(url, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(payload)
  })
  .then(res => {
    if (!res.ok) throw new Error();
    log(successMsg);
  })
  .catch(() => {
    log(errorMsg);
  });
}
</script>

</body>
</html>
