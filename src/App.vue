<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
const progress = ref(0)
const active = ref('intro')
const menuOpen = ref(false)
const isDark = ref(document.documentElement.dataset.theme === 'dark')
const themeMedia = window.matchMedia('(prefers-color-scheme: dark)')
let themeChosen = false
try { themeChosen = ['light', 'dark'].includes(localStorage.getItem('resume-theme')) } catch {}
const applyTheme = (dark) => {
 isDark.value = dark
 document.documentElement.dataset.theme = dark ? 'dark' : 'light'
 document.querySelector('meta[name="theme-color"]')?.setAttribute('content', dark ? '#101827' : '#edf1f8')
}
const toggleTheme = () => {
 themeChosen = true
 applyTheme(!isDark.value)
 try { localStorage.setItem('resume-theme', isDark.value ? 'dark' : 'light') } catch {}
}
const followSystemTheme = (event) => { if (!themeChosen) applyTheme(event.matches) }

const worlds = ['Jungle', 'Space', 'Sewers']
const scenes = ['Calm', 'Evolved', 'Chaotic', 'Boss']
const slides = worlds.flatMap(world => scenes.map(scene => ({
 world, scene, name: `${world.toLowerCase()}-${scene.toLowerCase()}`,
 alt: `Hopworlds — ${world} world, ${scene.toLowerCase()} scene`
})))
const slideIndex = ref(0)
const currentSlide = computed(() => slides[slideIndex.value])
const setSlide = (index) => { slideIndex.value = (index + slides.length) % slides.length }
let touchStart = null
const onSlideTouchStart = (event) => { touchStart = { x: event.changedTouches[0].clientX, y: event.changedTouches[0].clientY } }
const onSlideTouchEnd = (event) => {
 if (!touchStart) return
 const dx = event.changedTouches[0].clientX - touchStart.x
 const dy = event.changedTouches[0].clientY - touchStart.y
 if (Math.abs(dx) > 45 && Math.abs(dx) > Math.abs(dy)) setSlide(slideIndex.value + (dx < 0 ? 1 : -1))
 touchStart = null
}
const year = new Date().getFullYear()
const skills = ['Python','FastAPI','Django','Flask','Vue.js','JavaScript','TypeScript','PostgreSQL','Snowflake','Redis','Celery','Docker','React','Node.js','SQLAlchemy']
const roles = [
 { company: 'Snyk', role: 'Software Engineer', date: 'MAR 2022 — JUN 2026', tag: 'Security × analytics', intro: 'Making complex security data useful.', points: ['Built security analytics dashboards for vulnerability insights, CVE, CWE, and zero-day reporting.', 'Developed Python services and REST APIs, connecting Vue.js interfaces with Snowflake and PostgreSQL.', 'Delivered the backend for the Reporting Export API, helping customers export large security datasets.', 'Worked across engineering teams, contributed to interviews, and helped onboard engineers.'], stack: ['Python','Vue.js','Snowflake','PostgreSQL'] },
 { company: 'Topcoat', role: 'Software Engineer', date: 'OCT 2021 — MAR 2022', tag: 'Tools for builders', intro: 'A reporting platform you could build inside.', points: ['Built a browser-based reporting IDE with Vue.js and Monaco Editor.', 'Connected SQL and Vue code to custom reporting pages, with context-aware autocomplete.', 'Developed Python APIs and Snowflake-powered reporting experiences before the platform’s acquisition by Snyk.'], stack: ['Vue.js','Monaco Editor','Python','Snowflake'] },
 { company: 'Canvas Craft Media', role: 'Full Stack Developer', date: 'MAR 2019 — OCT 2021', tag: 'From idea to production', intro: 'Different problems. End-to-end ownership.', points: ['Delivered 10+ bespoke applications for international clients across e-commerce, media, automation, and business workflows.', 'Built Python scraping and automation solutions, video hosting applications, and an Electron activity tracker.', 'Worked directly with clients to turn requirements into production software.'], stack: ['Python','JavaScript','Electron','Full stack'] },
 { company: 'Independent development', role: 'Freelance Developer & Self-directed Learning', date: '2015 — 2019', tag: 'The foundation', intro: 'Learning by building for real people.', points: ['Built responsive websites and custom applications for local businesses and individual clients.', 'Translated Figma designs into interfaces using HTML, CSS, JavaScript, PHP, and Node.js.', 'Contributed to open source and participated in coding camps and technical workshops.'], stack: ['HTML / CSS','JavaScript','PHP','Node.js'] }
]
let observer, sectionObserver
const onScroll = () => { const max = document.documentElement.scrollHeight - innerHeight; progress.value = max > 0 ? scrollY / max : 0 }
const go = () => { menuOpen.value = false }
onMounted(() => {
 applyTheme(isDark.value); themeMedia.addEventListener('change', followSystemTheme)
 onScroll(); window.addEventListener('scroll', onScroll, { passive: true })
 observer = new IntersectionObserver(entries => entries.forEach(e => { if(e.isIntersecting) { e.target.classList.add('visible'); observer.unobserve(e.target) } }), { threshold: .08 })
 document.querySelectorAll('.reveal').forEach(el => observer.observe(el))
 sectionObserver = new IntersectionObserver(entries => entries.forEach(e => { if(e.isIntersecting) active.value = e.target.id }), { rootMargin: '-20% 0px -55% 0px' })
 document.querySelectorAll('main > section[id]').forEach(el => sectionObserver.observe(el))
})
onUnmounted(() => { themeMedia.removeEventListener('change', followSystemTheme); window.removeEventListener('scroll', onScroll); observer?.disconnect(); sectionObserver?.disconnect() })
</script>

<template>
 <div class="reading-progress" :style="{ transform: `scaleX(${progress})` }"></div>
 <header class="header">
  <a href="#intro" class="wordmark" aria-label="Gauravjeet Singh home" @click="go">gs<span class="brand-dot">.</span><span class="wordmark-label">ENGINEER / BUILDER</span></a>
  <div class="header-controls">
   <button class="theme-toggle" @click="toggleTheme" :aria-label="isDark ? 'Switch to light mode' : 'Switch to dark mode'" :aria-pressed="isDark" :title="isDark ? 'Switch to light mode' : 'Switch to dark mode'">
    <svg v-if="isDark" viewBox="0 0 24 24" aria-hidden="true"><circle cx="12" cy="12" r="4"/><path d="M12 2v2m0 16v2M2 12h2m16 0h2M5 5l1.5 1.5m11 11L19 19M5 19l1.5-1.5m11-11L19 5"/></svg>
    <svg v-else viewBox="0 0 24 24" aria-hidden="true"><path d="M20.5 14a9 9 0 0 1-10.5-10.5A9 9 0 1 0 20.5 14Z"/></svg>
   </button>
  <button class="menu-toggle" @click="menuOpen = !menuOpen" :aria-expanded="menuOpen" aria-controls="navigation">{{ menuOpen ? 'Close −' : 'Menu +' }}</button>
  </div>
  <nav id="navigation" :class="{ open: menuOpen }" aria-label="Main navigation">
   <a v-for="item in [['now','Now'],['work','Experience'],['toolkit','Toolkit']]" :key="item[0]" :href="`#${item[0]}`" :class="{ active: active === item[0] }" @click="go">{{ item[1] }}</a>
   <a href="mailto:gau94rav@gmail.com" class="nav-contact">Let’s connect <span>↗</span></a>
  </nav>
 </header>
 <main>
  <section id="intro" class="hero section-shell">
   <div class="hero-copy">
    <div class="eyebrow"><span class="status-dot"></span> GAURAVJEET SINGH <span class="eyebrow-divider">/</span> SOFTWARE ENGINEER</div>
    <h1>Engineering<br>the next<br><span class="outlined">what if.</span><span class="title-star" aria-hidden="true">✳</span></h1>
    <p class="hero-description">From security analytics to AI-assisted games.<br>I turn complex ideas into things people can use.</p>
    <div class="hero-actions"><a class="button primary" href="#now">Explore what I’m building <span>↓</span></a><a href="/gauravjeet-singh-resume.pdf" download class="text-link">Download resume <span>↗</span></a></div>
    <div class="hero-meta"><span>7+ YEARS IN SOFTWARE</span><span>FULL STACK. AI FOCUSED.</span></div>
   </div>
   <div class="hero-art" aria-label="Abstract AI network visualization">
    <div class="art-top"><span>IDEAS → SYSTEMS</span><span class="art-live"><i></i> BUILD MODE</span></div>
    <div class="orbital-scene" aria-hidden="true">
     <div class="orbit orbit-one"></div><div class="orbit orbit-two"></div><div class="orbit orbit-three"></div>
     <div class="orb-core"><svg viewBox="0 0 160 160"><path d="M80 12 140 46v68l-60 34-60-34V46Z M80 12v68m60-34L80 80l-60-34m60 34v68m-60-34 120-68M20 46l120 68"/></svg><span class="core-light"></span></div>
     <span class="orbit-node node-a">01<span>IMAGINE</span></span><span class="orbit-node node-b">02<span>BUILD</span></span><span class="orbit-node node-c">03<span>ITERATE</span></span>
    </div>
    <div class="art-bottom"><span>HUMAN CURIOSITY.<br>MACHINE POSSIBILITY.</span><span class="art-plus">+</span></div>
   </div>
   <div class="scroll-note"><span>SCROLL TO EXPLORE</span><span>↓</span></div>
  </section>
  <div class="transition-strip" aria-hidden="true"><span>THINK DEEPLY</span><span>✳</span><span>BUILD PLAYFULLY</span><span>✳</span><span>MAKE IT USEFUL</span><span>✳</span></div>
  <section id="now" class="section-shell now-section">
   <div class="section-heading reveal"><div class="eyebrow">01 / THE CURRENT CHAPTER</div><h2>New tools.<br>New possibilities.</h2><p>AI is now a big part of how I work.<br>These are the ideas I’m exploring today.</p></div>
   <div class="current-grid">
    <article class="project-card hopworlds-card reveal" aria-labelledby="hopworlds-title">
     <div class="card-label"><span>AI-ASSISTED CREATION / PROJECT 01</span><span class="project-status">UPCOMING GAME</span></div>
     <div class="hopworlds-layout">
      <div class="hopworlds-copy">
       <div class="eyebrow">GODOT · MOBILE ARCADE ADVENTURE</div>
       <h3 id="hopworlds-title">Hopworlds<span class="hop-title-dot">.</span></h3>
       <p class="hopworlds-tagline">Small jumps. Strange worlds.<br>Big adventures.</p>
       <div class="release-note"><span class="status-dot"></span> Coming to iOS and Android in Q4 2026.</div>
       <p>Hopworlds is a playful mobile arcade adventure where every bounce takes you deeper into a changing world. Launch from a slingshot, dodge mischievous creatures and discover unexpected powers across lush jungles, distant planets and winding sewers. Each journey builds toward a boss battle alongside a flying companion.</p>
       <p>Built in Godot, the game features Campaign, Arcade and Zen modes, touch and motion controls, and a custom Theme Studio that connects artwork, animation and audio to shared gameplay systems.</p>
       <div class="project-tags"><span>Godot</span><span>iOS + Android</span><span>Campaign · Arcade · Zen</span><span>Theme Studio</span></div>
      </div>
      <div class="hop-gallery" role="region" aria-roledescription="carousel" aria-label="Hopworlds screenshots" tabindex="0" @keydown.left.prevent="setSlide(slideIndex - 1)" @keydown.right.prevent="setSlide(slideIndex + 1)">
       <div class="world-picker" aria-label="Explore game worlds"><button v-for="(world,index) in worlds" :key="world" :class="{ selected: currentSlide.world === world }" :aria-pressed="currentSlide.world === world" @click="setSlide(index * 4)">{{ world }}</button></div>
       <div class="screenshot-stage" @touchstart.passive="onSlideTouchStart" @touchend.passive="onSlideTouchEnd">
        <img :src="`/hopworlds/${currentSlide.name}.webp`" :alt="currentSlide.alt" width="864" height="1440" decoding="async" loading="lazy" class="hop-screenshot">
       </div>
       <div class="slide-toolbar"><button class="slide-arrow" aria-label="Previous screenshot" @click="setSlide(slideIndex - 1)">←</button><div class="slide-caption" aria-live="polite" aria-atomic="true"><strong>{{ currentSlide.world }} / {{ currentSlide.scene }}</strong><span>{{ String(slideIndex + 1).padStart(2, '0') }} / 12</span></div><button class="slide-arrow" aria-label="Next screenshot" @click="setSlide(slideIndex + 1)">→</button></div>
       <div class="slide-thumbnails" aria-label="Choose a screenshot"><button v-for="(slide,index) in slides" :key="slide.name" :class="{ selected: slideIndex === index }" :aria-label="`Show ${slide.world} ${slide.scene} screenshot`" :aria-pressed="slideIndex === index" @click="setSlide(index)"><img :src="`/hopworlds/${slide.name}-thumb.webp`" alt="" width="120" height="200" loading="lazy" decoding="async"></button></div>
      </div>
     </div>
    </article>
    <article class="project-card risk-card reveal">
     <div class="card-label"><span>AI × INSURANCE</span><span class="project-status">IN DEVELOPMENT</span></div>
     <div class="risk-visual" aria-hidden="true"><div class="risk-ring ring-a"></div><div class="risk-ring ring-b"></div><div class="risk-shield"><svg viewBox="0 0 100 110"><path d="M50 8 86 22v30c0 24-17 41-36 50C31 93 14 76 14 52V22Z"/><path d="m33 54 12 12 24-27"/></svg></div><span class="risk-satellite sat-a">AI USAGE</span><span class="risk-satellite sat-b">RISK SIGNALS</span><span class="risk-satellite sat-c">ASSESSMENT</span></div>
     <div class="project-content"><span class="project-number">PROJECT / 02</span><h3>Understanding<br>the risk of AI.</h3><p>Working on an AI usage risk assessment project for insurance companies, helping them evaluate how their customers use AI.</p><div class="project-tags"><span>AI risk assessment</span><span>Insurance</span><span>Product development</span></div></div>
    </article>
   </div>
   <div class="side-project reveal"><span class="small-icon">↗</span><div><div class="eyebrow">ALSO ON THE WORKBENCH</div><h3>Smart Dashboard</h3><p>An independent analytics and reporting platform, bringing together dashboards, background processing, and data-driven workflows.</p></div><div class="side-stack">FastAPI · PostgreSQL<br>Celery · Docker</div></div>
  </section>
  <section id="work" class="work-section section-shell">
   <div class="work-intro reveal"><div class="eyebrow">02 / THE EXPERIENCE BEHIND IT</div><h2>A builder,<br>before the buzz.</h2><p>Years of shipping real software.<br>A foundation for what comes next.</p><div class="experience-metric"><strong>7<span>+</span></strong><span>YEARS OF PROFESSIONAL<br>ENGINEERING EXPERIENCE</span></div><a href="/gauravjeet-singh-resume.pdf" download class="text-link">The full resume <span>↗</span></a></div>
   <div class="timeline"><article v-for="(role,index) in roles" :key="role.company" class="role reveal"><div class="timeline-dot"></div><div class="role-top"><span>{{ role.date }}</span><span class="role-index">0{{ index + 1 }}</span></div><h3>{{ role.company }}</h3><div class="role-title">{{ role.role }}</div><div class="role-tag">{{ role.tag }}</div><h4>{{ role.intro }}</h4><ul><li v-for="point in role.points" :key="point">{{ point }}</li></ul><div class="role-stack"><span v-for="tech in role.stack" :key="tech">{{ tech }}</span></div></article></div>
  </section>
  <section id="toolkit" class="toolkit-section section-shell">
   <div class="section-heading reveal"><div class="eyebrow">03 / THE TOOLKIT</div><h2>Grounded in code.<br><span>Amplified by AI.</span></h2><p>Backend depth. Frontend craft.<br>A growing practice of building with AI tools.</p></div>
   <div class="toolkit-grid reveal"><div class="toolkit-feature"><span class="tool-symbol">✳</span><div class="eyebrow">HOW I WORK TODAY</div><h3>AI in the<br> building process.</h3><p>I work heavily with AI tools while developing new products, combining that approach with hands-on full-stack engineering.</p></div><div class="skill-panel"><div class="eyebrow">ENGINEERING FOUNDATION</div><div class="skill-cloud"><span v-for="skill in skills" :key="skill">{{ skill }}</span></div><div class="skill-focus"><div><span>01</span><h4>Backend & data</h4><p>APIs, reporting, aggregation, background jobs.</p></div><div><span>02</span><h4>Interfaces & products</h4><p>Dashboards, developer tools, full-stack applications.</p></div></div></div></div>
   <div class="education reveal"><span class="eyebrow">FOUNDATION</span><div><strong>B.Sc. in Information Technology</strong><span>Karnataka State Open University / NIIT</span></div><span>2012 — 2015</span></div>
  </section>
  <section id="contact" class="contact-section section-shell reveal"><div class="eyebrow"><span class="status-dot"></span> NEXT / A CONVERSATION</div><h2>Have an interesting<br><span>what if?</span></h2><div class="contact-bottom"><p>Let’s talk software, AI, games,<br>or the idea you can’t stop thinking about.</p><a class="button primary" href="mailto:gau94rav@gmail.com">gau94rav@gmail.com <span>↗</span></a></div></section>
 </main>
 <footer class="section-shell"><a class="wordmark" href="#intro">gs<span class="brand-dot">.</span></a><span>GAURAVJEET SINGH · {{ year }}</span><div class="footer-links"><a href="https://github.com/gau94rav" target="_blank" rel="noopener noreferrer">GitHub ↗</a><a href="https://linkedin.com/in/gaurav-singh-251979173" target="_blank" rel="noopener noreferrer">LinkedIn ↗</a><a href="#intro">Back to top ↑</a></div></footer>
</template>
