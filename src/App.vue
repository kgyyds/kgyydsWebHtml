<script setup>
import { computed, onMounted, onUnmounted, ref } from "vue";

const open = ref(false);
const active = ref("Home");

const nav = [
  { key: "Home", icon: "⌂", desc: "主页面板" },
  { key: "Tools", icon: "⚙", desc: "小工具箱" },
  { key: "Logs", icon: "≡", desc: "日志与状态" },
  { key: "About", icon: "i", desc: "关于本站" },
];

const toggle = () => (open.value = !open.value);
const close = () => (open.value = false);
const go = (k) => {
  active.value = k;
  close();
};

// ESC 关闭（桌面也舒服）
const onKey = (e) => {
  if (e.key === "Escape") close();
};

onMounted(() => window.addEventListener("keydown", onKey));
onUnmounted(() => window.removeEventListener("keydown", onKey));

const title = computed(() => {
  const m = nav.find((x) => x.key === active.value);
  return m ? m.key : "Home";
});

// 小小“灵动”欢迎语
const tip = computed(() => {
  switch (active.value) {
    case "Home":
      return "Welcome back. System ready ✅";
    case "Tools":
      return "Quick utilities. Fast & clean ⚡";
    case "Logs":
      return "Telemetry stream (mock). No panic 😎";
    case "About":
      return "Built with Vue3 + Vite + Pages 🖤";
    default:
      return "Ready.";
  }
});

// 假装的实时状态
const now = ref(new Date());
let t = null;
onMounted(() => {
  t = setInterval(() => (now.value = new Date()), 1000);
});
onUnmounted(() => t && clearInterval(t));

const timeText = computed(() => {
  const d = now.value;
  const hh = String(d.getHours()).padStart(2, "0");
  const mm = String(d.getMinutes()).padStart(2, "0");
  const ss = String(d.getSeconds()).padStart(2, "0");
  return `${hh}:${mm}:${ss}`;
});

// Tools demo state
const toolText = ref("");
const toolOut = ref("");

const doUpper = () => (toolOut.value = toolText.value.toUpperCase());
const doLower = () => (toolOut.value = toolText.value.toLowerCase());
const doClear = () => {
  toolText.value = "";
  toolOut.value = "";
};
</script>

<template>
  <div class="app" :class="{ 'menu-open': open }">
    <!-- 顶栏 -->
    <header class="top">
      <button class="burger" @click="toggle" aria-label="toggle menu">
        <span class="b"></span>
        <span class="b"></span>
        <span class="b"></span>
      </button>

      <div class="topTitle">
        <div class="t1">kgyyds://panel</div>
        <div class="t2">{{ title }} · <span class="muted">{{ timeText }}</span></div>
      </div>

      <div class="badge">ONLINE</div>
    </header>

    <!-- 遮罩：点一下收回 -->
    <div v-show="open" class="overlay" @click="close"></div>

    <!-- 侧边栏 -->
    <aside class="drawer" role="navigation" aria-label="sidebar">
      <div class="drawerHead">
        <div class="brand">
          <span class="dot"></span>
          <div>
            <div class="brandName">kgyyds</div>
            <div class="brandSub">mobile sidebar</div>
          </div>
        </div>
        <button class="x" @click="close" aria-label="close">×</button>
      </div>

      <div class="nav">
        <button
          v-for="m in nav"
          :key="m.key"
          class="navItem"
          :class="{ active: m.key === active }"
          @click="go(m.key)"
        >
          <div class="left">
            <div class="ico">{{ m.icon }}</div>
            <div class="txt">
              <div class="name">{{ m.key }}</div>
              <div class="desc">{{ m.desc }}</div>
            </div>
          </div>
          <div class="chev">›</div>
        </button>
      </div>

      <div class="drawerFoot">
        <div class="small muted">Tip: 点击遮罩 / ESC 都可收起</div>
        <div class="small muted">Deploy: Cloudflare Pages</div>
      </div>
    </aside>

    <!-- 主内容 -->
    <main class="main">
      <section class="hero">
        <div class="heroTop">
          <div class="pill">SECURE UI</div>
          <div class="pill muted">Vue3</div>
          <div class="pill muted">Mobile-first</div>
        </div>
        <h1 class="h1">{{ title }}</h1>
        <p class="p">{{ tip }}</p>
      </section>

      <section class="grid">
        <!-- Home -->
        <template v-if="active === 'Home'">
          <div class="card">
            <div class="cardTitle">Quick Actions</div>
            <div class="row">
              <button class="btn" @click="go('Tools')">Open Tools</button>
              <button class="btn ghost" @click="go('Logs')">View Logs</button>
            </div>
            <div class="hint muted">左上角按钮可随时呼出侧边栏。</div>
          </div>

          <div class="card">
            <div class="cardTitle">System</div>
            <div class="kv">
              <div class="k">Status</div><div class="v ok">ONLINE</div>
              <div class="k">Latency</div><div class="v">{{ 9 + Math.floor(Math.random()*12) }} ms</div>
              <div class="k">CPU</div><div class="v">{{ 22 + Math.floor(Math.random()*38) }}%</div>
              <div class="k">Memory</div><div class="v">{{ 35 + Math.floor(Math.random()*40) }}%</div>
            </div>
          </div>
        </template>

        <!-- Tools -->
        <template v-else-if="active === 'Tools'">
          <div class="card">
            <div class="cardTitle">Mini Tools</div>
            <div class="tool">
              <label class="lab">输入文本</label>
              <textarea class="ta" placeholder="随便输点…" v-model="toolText"></textarea>
              <div class="row">
                <button class="btn" @click="doUpper">UPPER</button>
                <button class="btn ghost" @click="doLower">lower</button>
                <button class="btn ghost" @click="doClear">clear</button>
              </div>
              <label class="lab">输出</label>
              <div class="out">{{ toolOut || "..." }}</div>
            </div>
          </div>

          <div class="card">
            <div class="cardTitle">Shortcuts</div>
            <div class="chips">
              <span class="chip">Base64（可加）</span>
              <span class="chip">Hash（可加）</span>
              <span class="chip">AES（可加）</span>
            </div>
            <div class="hint muted">你想加哪个工具，我就帮你补功能。</div>
          </div>
        </template>

        <!-- Logs -->
        <template v-else-if="active === 'Logs'">
          <div class="card">
            <div class="cardTitle">Logs</div>
            <div class="log">
              <div class="ln"><span class="tag">[OK]</span> Boot complete</div>
              <div class="ln"><span class="tag">[OK]</span> Network online</div>
              <div class="ln"><span class="tag">[OK]</span> Cache warmed</div>
              <div class="ln"><span class="tag warn">[!]</span> No domain bound (NS pending)</div>
              <div class="ln"><span class="tag">[OK]</span> Ready</div>
            </div>
          </div>

          <div class="card">
            <div class="cardTitle">Telemetry</div>
            <div class="kv">
              <div class="k">Time</div><div class="v">{{ timeText }}</div>
              <div class="k">Conn</div><div class="v ok">stable</div>
              <div class="k">Build</div><div class="v">vite</div>
              <div class="k">Edge</div><div class="v">global</div>
            </div>
          </div>
        </template>

        <!-- About -->
        <template v-else>
          <div class="card">
            <div class="cardTitle">About</div>
            <div class="about">
              <p>这是一个侧边栏布局模板：移动端优先，动效轻快。</p>
              <p>你可以把它扩成：工具站 / 文档站 / 控制面板 / 个人主页。</p>
              <p class="muted">需要我加路由（/tools /logs）也可以，或者接 Workers 做 API。</p>
            </div>
          </div>

          <div class="card">
            <div class="cardTitle">Next</div>
            <div class="chips">
              <span class="chip">vue-router</span>
              <span class="chip">Naive UI</span>
              <span class="chip">Workers API</span>
            </div>
          </div>
        </template>
      </section>

      <footer class="foot muted">
        © kgyyds · left sidebar demo
      </footer>
    </main>
  </div>
</template>

<style scoped>
.app {
  min-height: 100svh;
  background:
    radial-gradient(1000px 560px at 20% -10%, rgba(34,197,94,.18), transparent 55%),
    radial-gradient(900px 520px at 110% 10%, rgba(16,185,129,.12), transparent 60%),
    linear-gradient(180deg, #05070c 0%, #020409 60%, #01030a 100%);
  color: #d6ffe2;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  overflow-x: hidden;
}

.top {
  position: sticky;
  top: 0;
  z-index: 30;
  display: grid;
  grid-template-columns: 44px 1fr auto;
  align-items: center;
  gap: 10px;
  padding: 12px 12px;
  backdrop-filter: blur(10px);
  background: rgba(2,6,23,.55);
  border-bottom: 1px solid rgba(34,197,94,.16);
}

.burger {
  width: 44px;
  height: 38px;
  border-radius: 12px;
  border: 1px solid rgba(34,197,94,.22);
  background: rgba(1,4,12,.55);
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 5px;
  padding: 0 12px;
  transition: transform .18s ease;
}
.burger:active { transform: scale(.98); }
.b {
  height: 2px;
  border-radius: 999px;
  background: rgba(167,255,191,.95);
}
.topTitle .t1 { font-weight: 800; letter-spacing: .6px; color: #b7ffcd; }
.topTitle .t2 { font-size: 12px; opacity: .85; }
.badge {
  font-size: 12px;
  font-weight: 800;
  letter-spacing: .6px;
  padding: 6px 10px;
  border-radius: 999px;
  border: 1px solid rgba(34,197,94,.25);
  background: rgba(34,197,94,.10);
}

.overlay {
  position: fixed;
  inset: 0;
  z-index: 25;
  background: rgba(0,0,0,.55);
  animation: fade .18s ease;
}

.drawer {
  position: fixed;
  left: 0;
  top: 0;
  bottom: 0;
  z-index: 26;
  width: min(320px, 84vw);
  background: rgba(2,6,23,.92);
  border-right: 1px solid rgba(34,197,94,.18);
  transform: translateX(-105%);
  transition: transform .22s cubic-bezier(.2,.9,.2,1);
  box-shadow: 20px 0 50px rgba(0,0,0,.45);
  display: flex;
  flex-direction: column;
}
.menu-open .drawer { transform: translateX(0); }

.drawerHead {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 14px 14px 10px;
  border-bottom: 1px solid rgba(34,197,94,.14);
}
.brand { display: flex; align-items: center; gap: 10px; }
.dot {
  width: 10px; height: 10px; border-radius: 999px;
  background: #22c55e;
  box-shadow: 0 0 18px rgba(34,197,94,.55);
}
.brandName { font-weight: 900; letter-spacing: .5px; }
.brandSub { font-size: 12px; opacity: .65; }
.x {
  width: 38px; height: 38px;
  border-radius: 12px;
  border: 1px solid rgba(34,197,94,.18);
  background: rgba(1,4,12,.55);
  color: #b7ffcd;
  font-size: 18px;
}

.nav { padding: 10px; display: flex; flex-direction: column; gap: 8px; }
.navItem {
  border-radius: 14px;
  border: 1px solid rgba(34,197,94,.14);
  background: rgba(1,4,12,.45);
  padding: 12px 12px;
  color: inherit;
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: transform .14s ease, border-color .14s ease, background .14s ease;
}
.navItem:active { transform: scale(.99); }
.navItem.active {
  border-color: rgba(34,197,94,.35);
  background: rgba(34,197,94,.10);
}
.left { display: flex; align-items: center; gap: 12px; }
.ico {
  width: 34px; height: 34px;
  border-radius: 12px;
  border: 1px solid rgba(34,197,94,.20);
  display: grid; place-items: center;
  background: rgba(1,4,12,.55);
}
.txt .name { font-weight: 800; }
.txt .desc { font-size: 12px; opacity: .65; margin-top: 2px; }
.chev { opacity: .55; font-size: 18px; }

.drawerFoot {
  margin-top: auto;
  padding: 12px 14px 14px;
  border-top: 1px solid rgba(34,197,94,.12);
}

.main {
  width: min(920px, 100%);
  margin: 0 auto;
  padding: 14px 12px 22px;
}

.hero {
  border-radius: 18px;
  border: 1px solid rgba(34,197,94,.18);
  background: rgba(2,6,23,.55);
  box-shadow: 0 12px 40px rgba(0,0,0,.45);
  padding: 14px 14px 12px;
}
.heroTop { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 10px; }
.pill {
  font-size: 12px;
  padding: 6px 10px;
  border-radius: 999px;
  border: 1px solid rgba(34,197,94,.22);
  background: rgba(1,4,12,.45);
}
.h1 { margin: 0; font-size: 22px; letter-spacing: .4px; }
.p { margin: 8px 0 0; opacity: .82; }

.grid {
  margin-top: 12px;
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;
}
.card {
  border-radius: 18px;
  border: 1px solid rgba(34,197,94,.16);
  background: rgba(2,6,23,.50);
  padding: 14px;
  box-shadow: 0 10px 36px rgba(0,0,0,.38);
}
.cardTitle { font-weight: 900; letter-spacing: .6px; margin-bottom: 10px; }

.row { display: flex; gap: 10px; flex-wrap: wrap; }
.btn {
  border-radius: 14px;
  padding: 10px 14px;
  border: 1px solid rgba(34,197,94,.28);
  background: rgba(34,197,94,.12);
  color: #b7ffcd;
  font-weight: 900;
  letter-spacing: .4px;
}
.btn.ghost {
  background: rgba(1,4,12,.35);
  border-color: rgba(34,197,94,.18);
}
.hint { margin-top: 10px; font-size: 12px; }

.kv {
  display: grid;
  grid-template-columns: 120px 1fr;
  gap: 8px 10px;
  font-size: 13px;
}
.k { opacity: .65; }
.v { opacity: .92; }
.ok { color: #86efac; }

.log {
  border-radius: 14px;
  border: 1px solid rgba(34,197,94,.14);
  background: rgba(1,4,12,.35);
  padding: 10px 10px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.ln { font-size: 13px; display: flex; gap: 10px; }
.tag { color: #86efac; }
.warn { color: #fbbf24; }

.tool .lab { font-size: 12px; opacity: .70; display: block; margin: 8px 0 6px; }
.ta {
  width: 100%;
  min-height: 110px;
  border-radius: 14px;
  border: 1px solid rgba(34,197,94,.18);
  background: rgba(1,4,12,.45);
  color: #d6ffe2;
  padding: 10px 12px;
  outline: none;
  resize: vertical;
}
.out {
  border-radius: 14px;
  border: 1px dashed rgba(34,197,94,.20);
  background: rgba(1,4,12,.25);
  padding: 10px 12px;
  min-height: 44px;
  font-size: 13px;
  opacity: .92;
  word-break: break-word;
}
.chips { display: flex; gap: 8px; flex-wrap: wrap; }
.chip {
  font-size: 12px;
  padding: 6px 10px;
  border-radius: 999px;
  border: 1px solid rgba(34,197,94,.18);
  background: rgba(1,4,12,.35);
  opacity: .85;
}
.about p { margin: 8px 0; opacity: .88; }

.foot { text-align: center; margin-top: 14px; font-size: 12px; opacity: .7; }
.muted { color: rgba(167,255,191,.6); }

@keyframes fade {
  from { opacity: 0; }
  to { opacity: 1; }
}

@media (min-width: 860px) {
  .grid { grid-template-columns: 1fr 1fr; }
}
</style>