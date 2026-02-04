# PsExec Command Builder

Quickly build a PsExec command.  
Nothing is executed — this only generates a command you can copy and run manually.

---

## Target

<div class="psexec-grid">

<label class="important">
  Target Computer
  <input type="text" id="computer" placeholder="COMPUTER01">
</label>

</div>

---

## Presets

<button class="md-button md-button--primary" onclick="presetSystemCmd()">SYSTEM Interactive CMD</button>
<button class="md-button" onclick="presetBasicCmd()">Basic CMD</button>

---

## Options

<div class="psexec-grid">

<label>
  <input type="checkbox" id="accepteula">
  Accept EULA -accepteula
</label>

<label>
  <input type="checkbox" id="nobanner">
  No banner -nobanner
</label>

<label>
  <input type="checkbox" id="system">
  Run as SYSTEM -s
</label>

<label>
  <input type="checkbox" id="elevated">
  Elevated token -h
</label>

<label>
  <input type="checkbox" id="interactive">
  Interactive -i
</label>

<label>
  <input type="checkbox" id="dontwait">
  Don't wait -d
</label>

<label>
  <input type="checkbox" id="copy">
  Copy executable -c
</label>

<label>
  <input type="checkbox" id="forcecopy">
  Force copy -f
</label>

<label>
  Timeout (seconds) -n
  <input type="number" id="timeout" placeholder="30">
</label>

</div>

---

## Command

<div class="psexec-grid">

<label>
  Remote Command
  <input type="text" id="command" value="cmd.exe">
</label>

<label>
  Arguments
  <input type="text" id="arguments" placeholder="/c whoami">
</label>

</div>

---

## Generated Command

<textarea id="preview" rows="3" readonly style="width:100%; font-family: monospace;"></textarea>

<button class="md-button md-button--primary" onclick="copyCommand()">Copy to Clipboard</button>

---

<script>
function resetAll() {
  document.querySelectorAll("input[type=checkbox]").forEach(c => c.checked = false);
  document.querySelectorAll("input[type=text], input[type=number]").forEach(i => {
    if (i.id !== "computer") i.value = "";
  });
  command.value = "cmd.exe";
}

function build() {
  const p = [];
  p.push("PsExec.exe");

  if (accepteula.checked) p.push("-accepteula");
  if (nobanner.checked) p.push("-nobanner");
  if (system.checked) p.push("-s");
  if (elevated.checked) p.push("-h");
  if (interactive.checked) p.push("-i");
  if (dontwait.checked) p.push("-d");
  if (copy.checked) p.push("-c");
  if (forcecopy.checked) p.push("-f");
  if (timeout.value) p.push(`-n ${timeout.value}`);

  p.push(computer.value ? `\\\\${computer.value}` : "\\\\<computer>");
  p.push(command.value || "cmd.exe");

  if (arguments.value) p.push(arguments.value);

  preview.value = p.join(" ");
}

function presetSystemCmd() {
  resetAll();
  accepteula.checked = true;
  system.checked = true;
  interactive.checked = true;
  command.value = "cmd.exe";
  build();
}

function presetBasicCmd() {
  resetAll();
  accepteula.checked = true;
  command.value = "cmd.exe";
  build();
}

function copyCommand() {
  navigator.clipboard.writeText(preview.value);
}

document.querySelectorAll("input").forEach(el => {
  el.addEventListener("input", build);
});

build();
</script>

<style>
.psexec-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 0.75rem;
}

.psexec-grid label {
  display: flex;
  flex-direction: column;
  font-size: 0.9rem;
}

.important input {
  font-size: 1rem;
  font-weight: 600;
}
</style>
