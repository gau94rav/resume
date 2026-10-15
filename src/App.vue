<script setup>
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue'
import ScreenshotImage from './components/ScreenshotImage.vue'
const progress = ref(0)
const active = ref('intro')
const menuOpen = ref(false)
const isDark = ref(document.documentElement.dataset.theme === 'dark')
const applyTheme = (dark) => {
 isDark.value = dark
 document.documentElement.dataset.theme = dark ? 'dark' : 'light'
 document.querySelector('meta[name="theme-color"]')?.setAttribute('content', dark ? '#101827' : '#edf1f8')
}
const toggleTheme = () => {
 applyTheme(!isDark.value)
 try { localStorage.setItem('resume-theme', isDark.value ? 'dark' : 'light') } catch {}
}

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
const vulnFeatures = [
 { id: 'overview', label: 'Full preview', title: 'The full picture, before the details.', description: 'The Vuln Lens main page brings Focus Mode, environment and repository filters, scanner selection, prioritized findings, risk scores, and CSV exports into one workspace. Explore the focused views for a closer look at individual capabilities.', alt: 'Full Vuln Lens main dashboard showing its project header, Focus and Advanced modes, environment, repository and scanner filters, and complete top 10 priorities table.' },
 { id: 'priorities', label: 'Prioritize', title: 'Turn scan noise into a focused queue.', description: 'Focus Mode surfaces the next 10 priorities with severity, risk scores, repository context, occurrences, and status. Filtering and CSV exports help teams turn findings into actionable work.', alt: 'Vuln Lens Focus Mode showing the top 10 vulnerability priorities, risk scores, repository names, and CSV export.' },
 { id: 'remediation-groups', label: 'Group findings', title: 'Several advisories. One remediation target.', description: 'Related findings are grouped around a shared vulnerable package instance. This example brings eight advisories together, with severity, scores, and fixed versions visible alongside one dependency change.', alt: 'Vuln Lens shared remediation group for python-multipart, with eight related advisories and fixed versions.' },
 { id: 'ai-assistance', label: 'AI assistance', title: 'Explain the issue. Ground the next step.', description: 'Optional AI summaries draw on scanner evidence, occurrences, and issue history. Remediation guidance makes confidence visible; this example flags low confidence and offers guidance rather than automatic remediation.', alt: 'Vuln Lens AI analysis panel showing issue summaries, remediation guidance, and a low confidence indicator.' },
 { id: 'source-evidence', label: 'Trace evidence', title: 'Keep every finding connected to its source.', description: 'Source evidence preserves the branch, commit, scanner, scan-run link, and affected file. Teams can trace a dashboard finding back to the original scan and the code or dependency that needs attention.', alt: 'Vuln Lens source evidence showing a Git branch and commit, Trivy scan-run link, scanner type, and affected requirements file.' }
]
const vulnIndex = ref(0)
const vulnFeature = computed(() => vulnFeatures[vulnIndex.value])
const vulnDialog = ref(null)
const changeVulnFeature = (step) => { vulnIndex.value = (vulnIndex.value + step + vulnFeatures.length) % vulnFeatures.length }
const openVulnDetail = () => { vulnDialog.value?.showModal() }
const year = new Date().getFullYear()
const skills = ['Python','FastAPI','Django','Flask','Vue.js','JavaScript','TypeScript','PostgreSQL','Snowflake','Redis','Celery','Docker','React','Node.js','SQLAlchemy']
const learningProjects = [
 { id: 'tic-tac-toe', title: 'Tic Tac Toe', subtitle: 'A step beyond the basics', code: 'MZzgza', story: 'My first step into more advanced logic: taking turns, recognising a win, and making all the rules work together.', takeaway: 'Learning to think in states and rules.' },
 { id: 'calculator', title: 'Javascript Calculator', subtitle: 'It worked. Then I made it mine.', code: 'aQgMpw', story: 'A basic calculator was not enough for me. Once the maths worked, I added a theme to make it feel like my own.', takeaway: 'Functionality was the start. Personality came next.' },
 { id: 'drum-machine', title: 'Drum Machine', subtitle: 'Learning with a little rhythm', code: 'mQgLNp', story: 'A playful musical experiment: turning a grid of buttons into something I could make sounds with.', takeaway: 'A reminder that code can be playful.' },
 { id: 'stop-watch', title: 'Stop Watch', subtitle: 'Making time visible', code: 'maPNjO', story: 'A small exercise in timing and state, with start, stop, reset, and lap controls.', takeaway: 'Small tools. Practical lessons.' },
 { id: 'odometer', title: 'Odometer', subtitle: 'My first independent build at work', code: 'YMVLpe', story: 'In my first job, I was asked to make an animated number randomizer. The interesting rule: if a digit repeated at the same position, it had to stay still until that position changed again. Everything else needed to animate smoothly.', ending: 'It was the first project I built myself on the job - a small display, but a big personal milestone.', takeaway: 'Where learning became real work.', milestone: true }
]
const learningDialog = ref(null)
const selectedLearningProject = ref(null)
const openLearningScreenshot = async (project) => {
 selectedLearningProject.value = project
 await nextTick()
 learningDialog.value?.showModal()
}
const roles = [
 { company: 'Snyk', role: 'Software Engineer', date: 'MAR 2022 — JUN 2026', tag: 'Security × analytics', intro: 'Making complex security data useful.', points: ['Built security analytics dashboards for vulnerability insights, CVE, CWE, and zero-day reporting.', 'Developed Python services and REST APIs, connecting Vue.js interfaces with Snowflake and PostgreSQL.', 'Delivered the backend for the Reporting Export API, helping customers export large security datasets.', 'Worked across engineering teams, contributed to interviews, and helped onboard engineers.'], stack: ['Python','Vue.js','Snowflake','PostgreSQL'] },
 { company: 'Topcoat', role: 'Software Engineer', date: 'OCT 2021 — MAR 2022', tag: 'Tools for builders', intro: 'A reporting platform you could build inside.', points: ['Built a browser-based reporting IDE with Vue.js and Monaco Editor.', 'Connected SQL and Vue code to custom reporting pages, with context-aware autocomplete.', 'Developed Python APIs and Snowflake-powered reporting experiences before the platform’s acquisition by Snyk.'], stack: ['Vue.js','Monaco Editor','Python','Snowflake'] },
 { company: 'Canvas Craft Media', role: 'Full Stack Developer', date: 'MAR 2019 — OCT 2021', tag: 'From idea to production', intro: 'Different problems. End-to-end ownership.', points: ['Delivered 10+ bespoke applications for international clients across e-commerce, media, automation, and business workflows.', 'Built Python scraping and automation solutions, video hosting applications, and an Electron activity tracker.', 'Worked directly with clients to turn requirements into production software.'], stack: ['Python','JavaScript','Electron','Full stack'] },
 { company: 'Independent development', role: 'Freelance Developer & Self-directed Learning', date: '2015 — 2019', tag: 'The foundation', intro: 'Learning by building for real people.', points: ['Built responsive websites and custom applications for local businesses and individual clients.', 'Translated Figma designs into interfaces using HTML, CSS, JavaScript, PHP, and Node.js.', 'Contributed to open source and participated in coding camps and technical workshops.'], stack: ['HTML / CSS','JavaScript','PHP','Node.js'] }
]
const motionMedia = window.matchMedia('(prefers-reduced-motion: reduce)')
const booting = ref(!motionMedia.matches)
const bootSeed = Math.random().toString(16).slice(2, 8).toUpperCase()
const bootBands = Array.from({ length: 7 }, (_, index) => ({
 top: `${8 + index * 12 + Math.random() * 5}%`,
 height: `${2 + Math.random() * 12}px`,
 left: `${Math.random() * 30}%`,
 width: `${30 + Math.random() * 65}%`,
 animationDelay: `${Math.random() * 180}ms`
}))
let observer, sectionObserver, focusObserver, glitchObserver
let bootTimer
const glitchTimers = new Map()
const glitchFrames = new Map()
const glitchSeen = new WeakSet()
const glitchTargets = () => document.querySelectorAll('main > section[id], .project-card, .side-project, .toolkit-grid, .role')
const playGlitch = (element, entering) => {
 clearTimeout(glitchTimers.get(element))
 cancelAnimationFrame(glitchFrames.get(element))
 element.classList.remove('glitch-entering', 'glitch-leaving', 'glitched-away')
 if (motionMedia.matches) return
 element.style.setProperty('--glitch-x', `${(Math.random() > .5 ? 1 : -1) * (3 + Math.random() * 7)}px`)
 element.style.setProperty('--glitch-cut', `${12 + Math.random() * 50}%`)
 element.style.setProperty('--glitch-duration', `${entering ? 520 + Math.random() * 160 : 350 + Math.random() * 120}ms`)
 element.dataset.glitchVariant = String(Math.floor(Math.random() * 3))
 glitchFrames.set(element, requestAnimationFrame(() => {
  glitchFrames.delete(element)
  element.classList.add(entering ? 'glitch-entering' : 'glitch-leaving')
  glitchTimers.set(element, setTimeout(() => {
   element.classList.remove('glitch-entering', 'glitch-leaving')
   if (!entering) element.classList.add('glitched-away')
   glitchTimers.delete(element)
  }, 720))
 }))
}
const stopMotion = () => {
 if (!motionMedia.matches) return
 booting.value = false
 clearTimeout(bootTimer)
 glitchTimers.forEach(clearTimeout); glitchTimers.clear()
 glitchFrames.forEach(cancelAnimationFrame); glitchFrames.clear()
 glitchTargets().forEach(element => element.classList.remove('glitch-entering', 'glitch-leaving', 'glitched-away'))
}
const onScroll = () => { const max = document.documentElement.scrollHeight - innerHeight; progress.value = max > 0 ? scrollY / max : 0 }
const go = () => { menuOpen.value = false }
onMounted(() => {
 applyTheme(isDark.value)
 motionMedia.addEventListener('change', stopMotion)
 bootTimer = setTimeout(() => { booting.value = false }, 1000)
 glitchObserver = new IntersectionObserver(entries => entries.forEach(entry => {
  if (entry.isIntersecting) {
   glitchSeen.add(entry.target)
   if (!booting.value) playGlitch(entry.target, true)
  } else if (glitchSeen.has(entry.target)) playGlitch(entry.target, false)
 }), { rootMargin: '-12% 0px -12% 0px', threshold: 0 })
 glitchTargets().forEach(element => glitchObserver.observe(element))
 onScroll(); window.addEventListener('scroll', onScroll, { passive: true })
 observer = new IntersectionObserver(entries => entries.forEach(e => { if(e.isIntersecting) { e.target.classList.add('visible'); observer.unobserve(e.target) } }), { threshold: .08 })
 document.querySelectorAll('.reveal').forEach(el => observer.observe(el))
 sectionObserver = new IntersectionObserver(entries => entries.forEach(e => { if(e.isIntersecting) active.value = e.target.id }), { rootMargin: '-20% 0px -55% 0px' })
 document.querySelectorAll('main > section[id]').forEach(el => sectionObserver.observe(el))
 focusObserver = new IntersectionObserver(entries => entries.forEach(entry => {
  entry.target.classList.toggle('card-in-focus', entry.isIntersecting)
 }), { rootMargin: '-25% 0px -30% 0px', threshold: 0 })
 document.querySelectorAll('.project-card, .side-project, .toolkit-grid').forEach(el => focusObserver.observe(el))
})
onUnmounted(() => {
 clearTimeout(bootTimer); glitchObserver?.disconnect()
 motionMedia.removeEventListener('change', stopMotion)
 glitchTimers.forEach(clearTimeout); glitchFrames.forEach(cancelAnimationFrame)
 window.removeEventListener('scroll', onScroll); observer?.disconnect(); sectionObserver?.disconnect(); focusObserver?.disconnect() })
</script>

<template>
 <div v-if="booting" class="boot-glitch" aria-hidden="true">
  <div class="boot-grid"></div>
  <span v-for="(band,index) in bootBands" :key="index" class="boot-band" :style="band"></span>
  <div class="boot-signature"><span class="boot-mark">gs<span>.</span></span><span>ASSEMBLING THE POSSIBLE</span><small>{{ bootSeed }} / INITIALIZE</small></div>
 </div>
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
   <a v-for="item in [['now','Now'],['work','Experience'],['learning','Early work'],['toolkit','Toolkit']]" :key="item[0]" :href="`#${item[0]}`" :class="{ active: active === item[0] }" @click="go">{{ item[1] }}</a>
   <a href="mailto:gau94rav@gmail.com" class="nav-contact">Let’s connect <span>↗</span></a>
  </nav>
 </header>
 <main :class="{ 'boot-materializing': booting }">
  <section id="intro" :class="{ 'section-in-focus': active === 'intro' }" class="hero section-shell">
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
  <section id="now" :class="{ 'section-in-focus': active === 'now' }" class="section-shell now-section">
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
        <ScreenshotImage :key="currentSlide.name" :src="`/hopworlds/${currentSlide.name}.webp`" :alt="currentSlide.alt" width="864" height="1440" decoding="async" loading="lazy" class="hop-screenshot" />
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
   <article id="vuln-lens" class="project-card vuln-lens-card reveal" aria-labelledby="vuln-title">
    <div class="card-label"><span>DEVSECOPS / BACKEND ENGINEERING</span><span class="project-status">INDEPENDENT PROJECT</span></div>
    <div class="vuln-intro"><div><div class="eyebrow">FROM SECURITY SCANS TO CLEAR NEXT STEPS</div><h3 id="vuln-title">Vuln Lens<span class="hop-title-dot">.</span></h3></div><div class="vuln-visit"><a href="https://vuln-lens.vercel.app/" target="_blank" rel="noopener noreferrer" class="button primary">Explore Vuln Lens <span>↗</span></a><p class="vuln-hosting-note">Temporarily deployed on an unstable server. Moving to more reliable hosting soon.</p></div></div>
    <p class="vuln-summary">Built the backend for a DevSecOps vulnerability analytics platform that turns security scans into consistent, actionable findings. Teams can prioritize risk, track changes, preserve triage decisions, and move from detection to remediation across repositories.</p>
    <div class="vuln-flow" aria-label="Scan processing flow"><span>Security scanners</span><i aria-hidden="true">→</i><span>SARIF + API</span><i aria-hidden="true">→</i><span>Background workers</span><i aria-hidden="true">→</i><span>Findings + remediation</span></div>
    <div class="vuln-tour" role="region" aria-label="Vuln Lens feature tour">
     <div class="vuln-tour-bar"><span class="eyebrow">A CLOSER LOOK</span><span class="vuln-demo-label">PRODUCT INTERFACE · DEMO PROJECT</span></div>
     <div class="vuln-feature-picker" aria-label="Choose a Vuln Lens feature"><button v-for="(feature,index) in vulnFeatures" :key="feature.id" :aria-pressed="vulnIndex === index" :class="{ selected: vulnIndex === index }" @click="vulnIndex = index"><span>0{{ index + 1 }}</span>{{ feature.label }}</button></div>
     <figure class="vuln-figure"><div class="vuln-image-stage" :class="{ 'vuln-full-preview': vulnFeature.id === 'overview' }"><ScreenshotImage :key="vulnFeature.id" :src="`/vuln-lens/${vulnFeature.id}.webp`" :alt="vulnFeature.alt" loading="lazy" decoding="async" /><button class="vuln-expand" @click="openVulnDetail" aria-label="Enlarge feature screenshot">View larger ↗</button></div>
      <figcaption class="vuln-caption"><div aria-live="polite" aria-atomic="true"><span class="project-number">FEATURE {{ String(vulnIndex + 1).padStart(2, '0') }} / {{ String(vulnFeatures.length).padStart(2, '0') }}</span><h4>{{ vulnFeature.title }}</h4><p>{{ vulnFeature.description }}</p></div><div class="vuln-feature-arrows"><button class="slide-arrow" @click="changeVulnFeature(-1)" aria-label="Previous Vuln Lens feature">←</button><button class="slide-arrow" @click="changeVulnFeature(1)" aria-label="Next Vuln Lens feature">→</button></div></figcaption>
     </figure>
    </div>
    <div class="vuln-backend-notes"><div><span class="eyebrow">INGEST & NORMALIZE</span><p>SARIF uploads, webhooks, and GitHub Actions. Adapters for Trivy, CodeQL, Semgrep, and Snyk; deduplication, scan changes, and persistent issue history.</p></div><div><span class="eyebrow">CONNECT & REMEDIATE</span><p>GitHub App repository sync and pull requests for scanning workflows. Optional AI summaries and remediation PRs run as background jobs.</p></div><div><span class="eyebrow">SERVE & SECURE</span><p>Project-scoped accounts, authentication and recovery, repository summaries, remediation trends, notifications, and CSV/PDF exports.</p></div></div>
    <div class="vuln-footer"><div class="project-tags"><span>Python</span><span>FastAPI</span><span>SQLAlchemy</span><span>PostgreSQL</span><span>Celery</span><span>Redis</span><span>Docker Compose</span></div><p>Backend: API + processing services.<br>Frontend maintained separately.</p></div>
    <dialog ref="vulnDialog" class="vuln-dialog" aria-labelledby="vuln-dialog-title"><div class="vuln-dialog-header"><strong id="vuln-dialog-title">{{ vulnFeature.title }}</strong><button autofocus @click="vulnDialog.close()" aria-label="Close screenshot">Close ×</button></div><div class="vuln-dialog-image"><ScreenshotImage :key="vulnFeature.id" :src="`/vuln-lens/${vulnFeature.id}.webp`" :alt="vulnFeature.alt" decoding="async" /></div><p>{{ vulnFeature.description }}</p></dialog>
   </article>
  </section>
  <section id="work" :class="{ 'section-in-focus': active === 'work' }" class="work-section section-shell">
   <div class="work-intro reveal"><div class="eyebrow">02 / THE EXPERIENCE BEHIND IT</div><h2>A builder,<br>before the buzz.</h2><p>Years of shipping real software.<br>A foundation for what comes next.</p><div class="experience-metric"><strong>7<span>+</span></strong><span>YEARS OF PROFESSIONAL<br>ENGINEERING EXPERIENCE</span></div><a href="/gauravjeet-singh-resume.pdf" download class="text-link">The full resume <span>↗</span></a></div>
   <div class="timeline"><article v-for="(role,index) in roles" :key="role.company" class="role reveal"><div class="timeline-dot"></div><div class="role-top"><span>{{ role.date }}</span><span class="role-index">0{{ index + 1 }}</span></div><h3>{{ role.company }}</h3><div class="role-title">{{ role.role }}</div><div class="role-tag">{{ role.tag }}</div><h4>{{ role.intro }}</h4><ul><li v-for="point in role.points" :key="point">{{ point }}</li></ul><div class="role-stack"><span v-for="tech in role.stack" :key="tech">{{ tech }}</span></div></article></div>
  </section>
  <section id="learning" :class="{ 'section-in-focus': active === 'learning' }" class="section-shell learning-section">
   <div class="section-heading reveal"><div class="eyebrow">03 / EARLY EXPERIMENTS</div><h2>Where the<br><span>curiosity started.</span></h2><p>Small experiments from my learning days.<br>The same curiosity that still drives my work.</p></div>
   <div class="learning-grid">
    <article v-for="(project,index) in learningProjects" :key="project.id" :class="{ 'learning-milestone': project.milestone }" class="project-card learning-card reveal">
     <div class="learning-image" :class="`learning-image-${project.id}`"><ScreenshotImage :src="`/learning/${project.id}.jpg`" :alt="`${project.title}: screenshot of my original CodePen app`" loading="lazy" decoding="async" /><button class="vuln-expand" @click="openLearningScreenshot(project)" :aria-label="`Enlarge ${project.title} screenshot`">View larger ↗</button></div>
     <div class="learning-story"><div class="eyebrow"><span>0{{ index + 1 }} / {{ project.milestone ? 'THE FIRST-JOB MILESTONE' : 'LEARNING BY MAKING' }}</span></div><h3>{{ project.title }}</h3><h4>{{ project.subtitle }}</h4><p>{{ project.story }}</p><p v-if="project.ending">{{ project.ending }}</p><div class="learning-takeaway">{{ project.takeaway }}</div><a :href="`https://codepen.io/slave-of-machine/full/${project.code}`" target="_blank" rel="noopener noreferrer" class="text-link">Play with the original <span>↗</span></a></div>
    </article>
   </div>
   <dialog v-if="selectedLearningProject" ref="learningDialog" class="vuln-dialog learning-dialog" aria-labelledby="learning-dialog-title"><div class="vuln-dialog-header"><strong id="learning-dialog-title">{{ selectedLearningProject.title }} / The original experiment</strong><button autofocus @click="learningDialog.close()" aria-label="Close early project screenshot">Close ×</button></div><div class="vuln-dialog-image"><ScreenshotImage :src="`/learning/${selectedLearningProject.id}.jpg`" :alt="`${selectedLearningProject.title} app screenshot`" /></div><p>{{ selectedLearningProject.story }}</p><a :href="`https://codepen.io/slave-of-machine/full/${selectedLearningProject.code}`" target="_blank" rel="noopener noreferrer" class="text-link">Open the original on CodePen ↗</a></dialog>
  </section>
  <section id="toolkit" :class="{ 'section-in-focus': active === 'toolkit' }" class="toolkit-section section-shell">
   <div class="section-heading reveal"><div class="eyebrow">04 / THE TOOLKIT</div><h2>Grounded in code.<br><span>Amplified by AI.</span></h2><p>Backend depth. Frontend craft.<br>A growing practice of building with AI tools.</p></div>
   <div class="toolkit-grid reveal"><div class="toolkit-feature"><span class="tool-symbol">✳</span><div class="eyebrow">HOW I WORK TODAY</div><h3>AI in the<br> building process.</h3><p>I work heavily with AI tools while developing new products, combining that approach with hands-on full-stack engineering.</p></div><div class="skill-panel"><div class="eyebrow">ENGINEERING FOUNDATION</div><div class="skill-cloud"><span v-for="skill in skills" :key="skill">{{ skill }}</span></div><div class="skill-focus"><div><span>01</span><h4>Backend & data</h4><p>APIs, reporting, aggregation, background jobs.</p></div><div><span>02</span><h4>Interfaces & products</h4><p>Dashboards, developer tools, full-stack applications.</p></div></div></div></div>
  </section>
  <section id="contact" :class="{ 'section-in-focus': active === 'contact' }" class="contact-section section-shell"><div class="eyebrow"><span class="status-dot"></span> NEXT / A CONVERSATION</div><h2>Have an interesting<br><span>what if?</span></h2><div class="contact-bottom"><p>Let’s talk software, AI, games,<br>or the idea you can’t stop thinking about.</p><a class="button primary" href="mailto:gau94rav@gmail.com">gau94rav@gmail.com <span>↗</span></a></div></section>
 </main>
 <footer class="section-shell"><a class="wordmark" href="#intro">gs<span class="brand-dot">.</span></a><span>GAURAVJEET SINGH · {{ year }}</span><div class="footer-links"><a href="https://github.com/gau94rav" target="_blank" rel="noopener noreferrer">GitHub ↗</a><a href="https://linkedin.com/in/gaurav-singh-251979173" target="_blank" rel="noopener noreferrer">LinkedIn ↗</a><a href="#intro">Back to top ↑</a></div></footer>
</template>
