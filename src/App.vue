<script setup>
import { computed, onMounted, onUnmounted, ref } from "vue";

const now = ref(new Date());
let timer = null;

onMounted(() => {
  timer = setInterval(() => (now.value = new Date()), 1000);
});
onUnmounted(() => timer && clearInterval(timer));

const timeText = computed(() => {
  const d = now.value;
  const hh = String(d.getHours()).padStart(2, "0");
  const mm = String(d.getMinutes()).padStart(2, "0");
  const ss = String(d.getSeconds()).padStart(2, "0");
  return `${hh}:${mm}:${ss}`;
});

// “终端”打字机效果
const fullLines = [
  "[BOOT] Initializing secure shell...",
  "[OK]  Kernel hooks loaded.",
  "[OK]  Network: ONLINE",
  "[OK]  TLS: ENABLED",
  "[OK]  Ready. Type a command.",
];
const lines = ref([]);
const typingDone = ref(false);

const typeAll = async () => {
  lines.value = [];
  typingDone.value = false;
  for (const line of fullLines) {
    let s = "";
    for (const ch of line) {
      s += ch;
      const last = lines.value.length - 1;
      if (last >= 0 && lines.value[last].startsWith("[typing]")) {
        lines.value[last] = `[typing] ${s}`;
      } else {
        lines.value.push(`[typing] ${s}`);
      }
      await new Promise((r) => setTimeout(r, 12 + Math.random() * 28));
    }
    // 固化这一行
    lines.value[lines.value.length - 1] = line;
    await new Promise((r) => setTimeout(r, 140));
  }
  typingDone.value = true;
};

onMounted(() => {
  typeAll();
});

// 交互：输入命令（只是 UI 演示，不做真实执行）
const cmd = ref("");
const history = ref([]);
const run = () => {
  const input = cmd.value.trim();
  if (!input) return;

  history.value.push({ t: timeText.value, in: input, out: fakeRun(input) });
  cmd.value = "";
  // 滚动到底部（移动端更舒服）
  requestAnimationFrame(() => {
    const el = document.querySelector(".terminal");
    el && el.scrollTo({ top: el.scrollHeight, behavior: "smooth" });
  });
};

const quick = (c) => {
  cmd.value = c;
  run();
};

function fakeRun(input) {
  const low = input.toLowerCase();
  if (low === "help") {
    return [
      "commands:",
      "  status   - show system status",
      "  ping     - simulate ping",
      "  clear    - clear history",
      "  about    - show info",
      "  theme    - toggle neon glow (demo)",
    ];
  }
  if (low === "status") {
    return [
      "uptime: 00:" + String(Math.floor(Math.random() * 59)).padStart(2, "0") + ":" + String(Math.floor(Math.random() * 59)).padStart(2, "0"),
      "cpu: " + (18 + Math.floor(Math.random() * 36)) + "%",
      "mem: " + (33 + Math.floor(Math.random() * 45)) + "%",
      "net: ONLINE",
      "firewall: ACTIVE",
    ];
  }
  if (low === "ping") {
    return [
      "PING 1.1.1.1 (simulated)",
      "64 bytes from 1.1.1.1: icmp_seq=1 ttl=57 time=12.4 ms",
      "64 bytes from 1.1.1.1: icmp_seq=2 ttl=57 time=10.9 ms",
      "64 bytes from 1.1.1.1: icmp_seq=3 ttl=57 time=11.6 ms",
      "--- 1.1.1.1 ping statistics ---",
      "3 packets transmitted, 3 received, 0% packet loss",
    ];
  }
  if (low === "about") {
    return [
      "kgyydsWebHtml // mobile terminal UI",
      "Vue3 + Vite + Cloudflare Pages",
      "Tip: bind your domain when NS is ready 😉",
    ];
  }
  if (low === "clear") {
    history.value = [];
    return ["cleared."];
  }
  if (low === "theme") {
    glow.value = !glow.value;
    return [glow.value ? "neon glow: ON" : "neon glow: OFF"];
  }
  return [`unknown command: "${input}"`, `type "help" for options.`];
}

// 霓虹开关（演示）
const glow = ref(true);
</script>

<template>
  <div class="bg" :class="{ glow }">
    <header class="top">
      <div class="brand">
        <span class="dot"></span>
        <span class="name">kgyyds://console</span>
      </div>
      <div class="right">
        <span class="chip">5G</span>
        <span class="chip">VPN</span>
        <span class="time">{{ timeText }}</span>
      </div>
    </header>

    <main class="wrap">
      <section class="card">
        <div class="title">
          <span class="k">STATUS</span>
          <button class="mini" @click="typeAll" title="reboot">
            REBOOT
          </button>
        </div>

        <div class="terminal" aria-label="terminal">
          <div class="line" v-for="(l, i) in lines" :key="i">
            <span class="prompt">$</span>
            <span class="text" :class="{ typing: l.startsWith('[typing]') }">
              {{ l.replace('[typing] ', '') }}
            </span>
            <span v-if="i === lines.length - 1 && !typingDone" class="cursor">█</span>
          </div>

          <div v-if="history.length" class="sep"></div>

          <div v-for="(h, i) in history" :key="'h' + i" class="block">
            <div class="line">
              <span class="muted">[{{ h.t }}]</span>
              <span class="prompt">$</span>
              <span class="text">{{ h.in }}</span>
            </div>
            <div class="out">
              <div v-for="(o, j) in h.out" :key="i + '-' + j" class="oline">
                <span class="muted">></span>
                <span class="text">{{ o }}</span>
              </div>
            </div>
          </div>
        </div>

        <div class="inputRow">
          <div class="inWrap">
            <span class="prompt">$</span>
            <input
              v-model="cmd"
              class="input"
              placeholder='type "help" ...'
              autocomplete="off"
              autocapitalize="off"
              spellcheck="false"
              @keydown.enter.prevent="run"
            />
          </div>
          <button class="btn" @click="run">RUN</button>
        </div>

        <div class="quick">
          <button class="q" @click="quick('help')">help</button>
          <button class="q" @click="quick('status')">status</button>
          <button class="q" @click="quick('ping')">ping</button>
          <button class="q" @click="quick('about')">about</button>
          <button class="q" @click="quick('theme')">theme</button>
          <button class="q danger" @click="quick('clear')">clear</button>
        </div>
      </section>

      <footer class="foot">
        <span class="muted">©</span>
        <span class="muted">kgyyds</span>
        <span class="muted">|</span>
        <span class="muted">deploy: Cloudflare Pages</span>
      </footer>
    </main>
  </div>
</template>

<style scoped>
/* ====== 全局布局（移动端优先） ====== */
.bg {
  min-height: 100svh;
  background:
    radial-gradient(1200px 600px at 20% -10%, rgba(34, 197, 94, 0.20), transparent 55%),
    radial-gradient(900px 500px at 110% 10%, rgba(16, 185, 129, 0.14), transparent 60%),
    linear-gradient(180deg, #05070c 0%, #020409 60%, #01030a 100%);
  color: #caffd8;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  display: flex;
  flex-direction: column;
}

.top {
  position: sticky;
  top: 0;
  z-index: 20;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 14px 14px 10px;
  backdrop-filter: blur(10px);
  background: rgba(2, 6, 23, 0.55);
  border-bottom: 1px solid rgba(34, 197, 94, 0.18);
}

.brand {
  display: flex;
  align-items: center;
  gap: 10px;
}
.dot {
  width: 10px;
  height: 10px;
  border-radius: 999px;
  background: #22c55e;
  box-shadow: 0 0 18px rgba(34, 197, 94, 0.6);
}
.name {
  font-weight: 700;
  letter-spacing: 0.4px;
  color: #b7ffcd;
  opacity: 0.95;
}

.right {
  display: flex;
  align-items: center;
  gap: 8px;
}
.chip {
  font-size: 12px;
  padding: 4px 8px;
  border: 1px solid rgba(34, 197, 94, 0.22);
  background: rgba(2, 6, 23, 0.55);
  border-radius: 999px;
  color: #b7ffcd;
  opacity: 0.92;
}
.time {
  font-size: 12px;
  opacity: 0.85;
}

/* ====== 内容区 ====== */
.wrap {
  width: min(720px, 100%);
  margin: 0 auto;
  padding: 14px 12px 22px;
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.card {
  border-radius: 18px;
  border: 1px solid rgba(34, 197, 94, 0.20);
  background: rgba(2, 6, 23, 0.55);
  box-shadow: 0 12px 40px rgba(0,0,0,0.45);
  overflow: hidden;
}

.title {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 14px 14px 10px;
  border-bottom: 1px solid rgba(34, 197, 94, 0.16);
}
.k {
  font-weight: 800;
  letter-spacing: 1px;
  color: #a7ffbf;
}
.mini {
  font-size: 12px;
  padding: 6px 10px;
  border-radius: 999px;
  border: 1px solid rgba(34, 197, 94, 0.25);
  background: rgba(1, 4, 12, 0.6);
  color: #b7ffcd;
}

/* ====== 终端区 ====== */
.terminal {
  padding: 12px 14px;
  height: 52svh;         /* 手机看起来像 App */
  max-height: 560px;
  overflow: auto;
  scroll-behavior: smooth;
}

.line {
  display: flex;
  align-items: baseline;
  gap: 8px;
  line-height: 1.55;
  font-size: 13px;
  word-break: break-word;
}
.prompt {
  color: #22c55e;
  opacity: 0.95;
}
.text {
  color: #d6ffe2;
  opacity: 0.92;
}
.text.typing {
  opacity: 0.85;
}
.cursor {
  color: #22c55e;
  opacity: 0.95;
  animation: blink 1s step-start infinite;
}

.sep {
  margin: 12px 0;
  border-top: 1px dashed rgba(34, 197, 94, 0.22);
}

.block {
  margin: 10px 0 12px;
}
.out {
  margin-left: 14px;
  margin-top: 6px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.oline {
  display: flex;
  gap: 8px;
  font-size: 12.5px;
}
.muted {
  color: rgba(167, 255, 191, 0.55);
}

/* ====== 输入区 ====== */
.inputRow {
  display: grid;
  grid-template-columns: 1fr auto;
  gap: 10px;
  padding: 12px 14px 10px;
  border-top: 1px solid rgba(34, 197, 94, 0.14);
}

.inWrap {
  display: flex;
  align-items: center;
  gap: 10px;
  border-radius: 14px;
  border: 1px solid rgba(34, 197, 94, 0.22);
  background: rgba(1, 4, 12, 0.55);
  padding: 10px 12px;
}

.input {
  width: 100%;
  border: none;
  outline: none;
  background: transparent;
  color: #d6ffe2;
  font-size: 14px;
  caret-color: #22c55e;
}

.btn {
  border-radius: 14px;
  padding: 10px 14px;
  border: 1px solid rgba(34, 197, 94, 0.28);
  background: rgba(34, 197, 94, 0.12);
  color: #b7ffcd;
  font-weight: 800;
  letter-spacing: 0.6px;
}

.quick {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  padding: 10px 14px 14px;
}
.q {
  font-size: 12px;
  padding: 7px 10px;
  border-radius: 999px;
  border: 1px solid rgba(34, 197, 94, 0.22);
  background: rgba(1, 4, 12, 0.5);
  color: #b7ffcd;
}
.q.danger {
  border-color: rgba(239, 68, 68, 0.35);
  color: rgba(255, 198, 198, 0.95);
}

/* ====== 页脚 ====== */
.foot {
  text-align: center;
  padding: 6px 0 0;
  font-size: 12px;
  opacity: 0.75;
}

/* 霓虹增强（可切换） */
.glow .card {
  box-shadow:
    0 12px 40px rgba(0,0,0,0.45),
    0 0 0 1px rgba(34, 197, 94, 0.15),
    0 0 24px rgba(34, 197, 94, 0.12);
}
.glow .terminal {
  text-shadow: 0 0 18px rgba(34, 197, 94, 0.08);
}

@keyframes blink {
  50% { opacity: 0.15; }
}

/* 桌面适配稍微拉宽一点 */
@media (min-width: 768px) {
  .terminal { height: 420px; }
  .line { font-size: 14px; }
}
</style>