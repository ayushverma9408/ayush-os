<script lang="ts">
  import { ArrowDown, ArrowUpRight, Terminal, Github, Linkedin, Mail, Code2, Cpu, Globe, ExternalLink, GraduationCap, GitBranch, Menu, X, Sparkles, Braces, Database, Layers3 } from 'lucide-svelte';
  import StatsDashboard from '../lib/StatsDashboard.svelte';

  let menuOpen = $state(false);
  let command = $state('');
  let terminalOutput = $state('Welcome to AYUSH OS. Type `help` to explore.');
  let booted = $state(false);

  const projects = [
    {
      name: 'Hostel Allotment System',
      type: 'WEB APPLICATION',
      status: 'BUILDING',
      description: 'A room-allocation workflow with capacity checks, waitlists, spreadsheet import/export and a live preview.',
      stack: ['SvelteKit', 'TypeScript', 'Tailwind', 'SheetJS'],
      link: 'https://github.com/ayushverma9408/Hacakathon-hostel-allotment-system',
      number: '01'
    },
    {
      name: 'JEE Advanced Paper Archive',
      type: 'EDUCATION',
      status: 'EARLY STAGE',
      description: 'A growing archive project focused on making past JEE Advanced papers easier to find and explore.',
      stack: ['Web', 'Education', 'Open Source'],
      link: 'https://github.com/ayushverma9408/JEE-ADVANCED-PAPER-ARCHIVE',
      number: '02'
    },
    {
      name: 'FloodWatch',
      type: 'HACKATHON CONCEPT',
      status: 'CONCEPT',
      description: 'A proposed hardware + software flood-monitoring concept combining sensor data with a digital dashboard.',
      stack: ['IoT', 'Dashboard', 'Data'],
      link: 'https://github.com/ayushverma9408',
      number: '03'
    },
    {
      name: 'Competitive Programming',
      type: 'ALGORITHM PRACTICE',
      status: 'ONGOING',
      description: 'A collection of problem-solving practice, algorithm implementations and contest learnings in C++.',
      stack: ['C++', 'DSA', 'Algorithms'],
      link: 'https://github.com/ayushverma9408/Codeforces_9408',
      number: '04'
    }
  ];

  const skillGroups = [
    { icon: Code2, title: 'LANGUAGES', items: ['C++', 'Python', 'JavaScript', 'TypeScript', 'SQL'] },
    { icon: Braces, title: 'PROBLEM SOLVING', items: ['Data Structures', 'Algorithms', 'Competitive Programming', 'OOP'] },
    { icon: Globe, title: 'WEB DEVELOPMENT', items: ['HTML', 'CSS', 'SvelteKit', 'Tailwind CSS', 'REST APIs'] },
    { icon: Cpu, title: 'EXPLORING', items: ['Machine Learning', 'Data Science', 'NLP', 'IoT'] }
  ];

  const focus = ['Strengthening DSA & algorithmic thinking', 'Building useful web applications', 'Exploring AI, ML and systems'];

  function runCommand() {
    const value = command.trim().toLowerCase();
    const responses: Record<string, string> = {
      help: 'Available commands: about · projects · skills · contact · clear',
      about: 'CSE undergrad at NIT Silchar. I enjoy algorithms, building for the web and learning by shipping.',
      projects: '4 project tracks loaded. Scroll to PROJECT ARCHIVE to inspect them.',
      skills: 'C++ / Python / TypeScript / SvelteKit / DSA / Web Development / ML exploration.',
      contact: 'Email: ayushverma19341@gmail.com · GitHub: @ayushverma9408',
      clear: ''
    };
    terminalOutput = responses[value] ?? `Command not found: ${value}. Type "help" for available commands.`;
    command = '';
  }
</script>

<svelte:head>
  <title>Ayush Verma — AYUSH OS</title>
  <meta name="description" content="Ayush Verma — Computer Science undergraduate, problem solver and developer building at the intersection of algorithms and the web." />
  <meta property="og:title" content="Ayush Verma — AYUSH OS" />
  <meta property="og:description" content="Developer portfolio · NIT Silchar CSE" />
</svelte:head>

<div class="site-shell">
  <div class="scanlines"></div>
  <header class="topbar">
    <a class="brand" href="#home" aria-label="Ayush OS home"><span class="brand-mark">A<span>.</span></span><span>AYUSH<span class="muted">_OS</span></span></a>
    <nav class:open={menuOpen}>
      <a href="#about" onclick={() => menuOpen = false}>ABOUT</a>
      <a href="#projects" onclick={() => menuOpen = false}>PROJECTS</a>
      <a href="#skills" onclick={() => menuOpen = false}>SKILLS</a>
      <a href="#connect" onclick={() => menuOpen = false}>CONTACT</a>
    </nav>
    <div class="top-right"><span class="online-dot"></span><span>ALL SYSTEMS LEARNING</span></div>
    <button class="menu-toggle" aria-label="Toggle menu" onclick={() => menuOpen = !menuOpen}>{#if menuOpen}<X size={20}/>{:else}<Menu size={20}/>{/if}</button>
  </header>

  <main id="home">
    <section class="hero section-wrap">
      <div class="hero-grid"></div>
      <div class="hero-copy">
        <div class="eyebrow"><span class="prompt-symbol">▸</span> PORTFOLIO INTERFACE <span class="eyebrow-line"></span> SYS.VERSION 1.0</div>
        <p class="hero-kicker">HELLO, WORLD. I'M</p>
        <h1>AYUSH<br/><span>VERMA<span class="cursor">_</span></span></h1>
        <div class="role-line"><span class="role-bracket">[</span> CSE UNDERGRAD <span class="role-divider">/</span> DEVELOPER <span class="role-divider">/</span> PROBLEM SOLVER <span class="role-bracket">]</span></div>
        <p class="hero-description">I turn curiosity into code — exploring algorithms, building for the web, and figuring out how things work under the hood.</p>
        <div class="hero-actions">
          <a class="button-primary" href="#projects">EXPLORE PROJECTS <ArrowUpRight size={16}/></a>
          <a class="button-ghost" href="https://github.com/ayushverma9408" target="_blank" rel="noreferrer"><Github size={16}/> GITHUB PROFILE</a>
        </div>
        <div class="hero-meta"><span><span class="meta-dot"></span> BASED IN INDIA</span><span>OPEN TO LEARNING & COLLABORATION</span></div>
      </div>
      <div class="hero-console">
        <div class="console-top"><div class="window-dots"><i></i><i></i><i></i></div><span>ayush@portfolio: ~</span><span class="console-live">LIVE</span></div>
        <div class="console-body">
          <p class="console-muted">AYUSH OS [Version 1.0.26]</p>
          <p class="console-muted">(c) 2026 — Built with curiosity.</p>
          <div class="console-gap"></div>
          <p><span class="console-green">ayush@os</span>:<span class="console-blue">~</span>$ whoami</p>
          <p class="console-output">Computer Science undergraduate<br/>NIT Silchar · Class of 2029</p>
          <p><span class="console-green">ayush@os</span>:<span class="console-blue">~</span>$ cat interests.txt</p>
          <p class="console-output">algorithms<br/>web development<br/>AI / ML exploration<br/>building useful things</p>
          <p><span class="console-green">ayush@os</span>:<span class="console-blue">~</span>$ <span class="console-cursor">█</span></p>
          <div class="console-status"><span><span class="meta-dot"></span> SYSTEM ONLINE</span><span>INDIA / IST</span></div>
        </div>
      </div>
      <a class="scroll-cue" href="#about"><span>SCROLL TO INITIALIZE</span><ArrowDown size={15}/></a>
    </section>

    <section class="section-wrap about-section" id="about">
      <div class="section-heading"><span class="section-index">01 //</span><h2>ABOUT <span>ME</span></h2><span class="heading-rule"></span><span class="section-tag">IDENTITY.MODULE</span></div>
      <div class="about-grid">
        <div class="about-main">
          <div class="section-label"><span class="tiny-square"></span> THE HUMAN BEHIND THE KEYBOARD</div>
          <h3>Curious by default.<br/><span>Builder by choice.</span></h3>
          <p>I'm a Computer Science undergraduate at <strong>National Institute of Technology, Silchar</strong>. I like understanding the logic behind things — from data structures and algorithms to the systems that power the web.</p>
          <p>I'm still early in the journey, and that's the exciting part. I learn by experimenting, solving problems, collaborating, and turning ideas into working software.</p>
          <div class="about-tags"><span>#BUILDINPUBLIC</span><span>#LEARNBYDOING</span><span>#STAYCURIOUS</span></div>
        </div>
        <div class="info-panel">
          <div class="panel-head"><span>PROFILE.SYS</span><span class="panel-id">ID_001</span></div>
          <div class="info-row"><span>NAME</span><strong>Ayush Verma</strong></div>
          <div class="info-row"><span>EDUCATION</span><strong>B.Tech · Computer Science</strong></div>
          <div class="info-row"><span>INSTITUTE</span><strong>NIT Silchar</strong></div>
          <div class="info-row"><span>GRADUATION</span><strong>2029</strong></div>
          <div class="info-row"><span>LOCATION</span><strong>India</strong></div>
          <div class="info-row"><span>CURRENT MODE</span><strong class="green-text">LEARNING & BUILDING</strong></div>
          <div class="panel-foot"><span>PROFILE LOADED</span><span>100%</span></div><div class="progress-track"><span></span></div>
        </div>
      </div>
    </section>

    <section class="section-wrap focus-section">
      <div class="section-heading"><span class="section-index">02 //</span><h2>CURRENT <span>OBJECTIVES</span></h2><span class="heading-rule"></span><span class="section-tag">ROADMAP.LOG</span></div>
      <div class="focus-grid">
        {#each focus as item, i}
          <div class="focus-card"><span class="focus-num">0{i + 1}</span><span>{item}</span><ArrowUpRight size={17}/></div>
        {/each}
      </div>
    </section>

    <section class="section-wrap projects-section" id="projects">
      <div class="section-heading"><span class="section-index">03 //</span><h2>PROJECT <span>ARCHIVE</span></h2><span class="heading-rule"></span><span class="section-tag">SELECTED.WORKS</span></div>
      <div class="section-intro"><p>A few things I've worked on, explored, or am actively developing.</p><span>04 RECORDS FOUND</span></div>
      <div class="projects-grid">
        {#each projects as project}
          <article class="project-card">
            <div class="project-top"><span>PROJECT_{project.number}</span><span class="project-status"><i></i>{project.status}</span></div>
            <div class="project-icon">{#if project.number === '01'}<Layers3 size={23}/>{:else if project.number === '02'}<GraduationCap size={23}/>{:else if project.number === '03'}<Cpu size={23}/>{:else}<GitBranch size={23}/>{/if}</div>
            <p class="project-type">{project.type}</p>
            <h3>{project.name}</h3>
            <p class="project-description">{project.description}</p>
            <div class="stack-list">{#each project.stack as item}<span>{item}</span>{/each}</div>
            <a class="project-link" href={project.link} target="_blank" rel="noreferrer">VIEW PROJECT <ArrowUpRight size={15}/></a>
          </article>
        {/each}
      </div>
    </section>

    <section class="section-wrap skills-section" id="skills">
      <div class="section-heading"><span class="section-index">04 //</span><h2>SKILL <span>MATRIX</span></h2><span class="heading-rule"></span><span class="section-tag">TECH.STACK</span></div>
      <p class="skills-note">A snapshot of technologies and topics I'm learning and working with — not a claim of mastery.</p>
      <div class="skills-grid">
        {#each skillGroups as group, i}
          <div class="skill-card"><div class="skill-card-head"><svelte:component this={group.icon} size={19}/><span>MODULE_0{i + 1}</span></div><h3>{group.title}</h3><div class="skill-items">{#each group.items as item}<span>{item}</span>{/each}</div></div>
        {/each}
      </div>
    </section>

    <section class="section-wrap terminal-section">
      <div class="section-heading"><span class="section-index">05 //</span><h2>INTERACTIVE <span>TERMINAL</span></h2><span class="heading-rule"></span><span class="section-tag">SHELL.ACCESS</span></div>
      <div class="terminal-window">
        <div class="terminal-title"><div class="window-dots"><i></i><i></i><i></i></div><span>guest@ayush-os — interactive shell</span><span class="terminal-lock">READ ONLY</span></div>
        <div class="terminal-content">
          <p class="terminal-welcome">AYUSH OS interactive shell · Type <b>help</b> to see available commands.</p>
          <p class="terminal-prompt"><span>guest@ayush-os</span>:~$ <span>{command || ' '}</span></p>
          <p class="terminal-response">{terminalOutput}</p>
          <form class="terminal-form" onsubmit={(e) => { e.preventDefault(); runCommand(); }}>
            <span>guest@ayush-os:~$</span>
            <input aria-label="Terminal command" bind:value={command} placeholder="type a command..." autocomplete="off" />
            <button type="submit" aria-label="Run command"><ArrowUpRight size={16}/></button>
          </form>
        </div>
      </div>
      <p class="terminal-hint"><span class="meta-dot"></span> SAFE SANDBOX · Commands only navigate this portfolio. No system commands are executed.</p>
    </section>

    <section class="section-wrap coding-section">
      <div class="section-heading"><span class="section-index">06 //</span><h2>CODING <span>PROFILES</span></h2><span class="heading-rule"></span><span class="section-tag">PLAYER.STATS</span></div>
      <div class="coding-grid">
        <a class="coding-card" href="https://leetcode.com/u/ayushverma9408/" target="_blank" rel="noreferrer"><div class="coding-icon">LC</div><div><span class="coding-label">PROBLEM SOLVING</span><h3>LeetCode</h3><p>Practice · Patterns · Consistency</p></div><ExternalLink size={17}/></a>
        <a class="coding-card" href="https://codeforces.com/profile/ayushverma9408" target="_blank" rel="noreferrer"><div class="coding-icon cf">CF</div><div><span class="coding-label">CONTEST ARENA</span><h3>Codeforces</h3><p>Algorithms · Contests · C++</p></div><ExternalLink size={17}/></a>
        <a class="coding-card" href="https://www.codechef.com/users/ayushverma9408" target="_blank" rel="noreferrer"><div class="coding-icon cc">CC</div><div><span class="coding-label">COMPETITIVE CODING</span><h3>CodeChef</h3><p>Problem solving · Contests</p></div><ExternalLink size={17}/></a>
      </div>
    </section>

    <section class="section-wrap live-stats-section" id="live-stats">
      <div class="section-heading"><span class="section-index">07 //</span><h2>LIVE <span>STATISTICS</span></h2><span class="heading-rule"></span><span class="section-tag">PLATFORM.FEED</span></div>
      <StatsDashboard />
    </section>

    <section class="section-wrap connect-section" id="connect">
      <div class="connect-panel">
        <div class="connect-orbit orbit-one"></div><div class="connect-orbit orbit-two"></div>
        <div class="section-label"><span class="tiny-square"></span> END OF TRANSMISSION? NOT YET.</div>
        <h2>LET'S BUILD<br/><span>SOMETHING</span><br/>MEANINGFUL<span class="cursor">_</span></h2>
        <p>Have an interesting idea, a project to collaborate on, or just want to talk tech? My inbox is open.</p>
        <div class="connect-actions"><a class="button-primary" href="mailto:ayushverma19341@gmail.com"><Mail size={16}/> INITIATE CONTACT <ArrowUpRight size={15}/></a><a class="button-ghost" href="https://www.linkedin.com/in/ayushverma9408/" target="_blank" rel="noreferrer"><Linkedin size={16}/> LINKEDIN</a></div>
      </div>
    </section>
  </main>

  <footer class="footer section-wrap">
    <a class="brand" href="#home"><span class="brand-mark">A<span>.</span></span><span>AYUSH<span class="muted">_OS</span></span></a>
    <p>DESIGNED IN CURIOSITY. BUILT WITH CODE.</p>
    <a href="https://github.com/ayushverma9408" target="_blank" rel="noreferrer">GITHUB <ArrowUpRight size={13}/></a>
    <span class="copyright">© 2026 AYUSH VERMA</span>
  </footer>
</div>

<style>
  :global(html){scroll-behavior:smooth;scroll-padding-top:90px}
  :global(body){margin:0;background:#070b12;color:#e8edf5;font-family:Inter,ui-sans-serif,system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif}
  :global(*){box-sizing:border-box}
  .site-shell{min-height:100vh;overflow:hidden;background:radial-gradient(ellipse at 75% 7%,rgba(0,225,170,.07),transparent 29%),#070b12;position:relative}
  .scanlines{position:fixed;inset:0;pointer-events:none;z-index:5;opacity:.12;background:repeating-linear-gradient(0deg,transparent,transparent 3px,rgba(255,255,255,.025) 4px)}
  .section-wrap{width:min(1120px,calc(100% - 48px));margin-inline:auto}
  .topbar{height:76px;border-bottom:1px solid #1b2933;display:flex;align-items:center;justify-content:space-between;padding:0 max(24px,calc((100vw - 1120px)/2));position:sticky;top:0;z-index:20;background:rgba(7,11,18,.88);backdrop-filter:blur(16px)}
  .brand{display:flex;align-items:center;gap:10px;color:#e8edf5;text-decoration:none;font-size:13px;font-weight:850;letter-spacing:2px}.brand-mark{font-size:25px;color:#00e6a8;letter-spacing:-2px}.brand-mark span{color:#e8edf5}.muted{color:#82909d}
  nav{display:flex;gap:34px}nav a,.top-right,.eyebrow,.hero-meta,.section-index,.section-tag,.section-label,.project-top,.project-type,.project-link,.panel-head,.info-row>span,.panel-foot,.focus-num,.coding-label,.footer>p,.footer>a:not(.brand),.copyright{font-family:"SFMono-Regular",Consolas,monospace;font-size:10px;letter-spacing:1.2px}
  nav a{color:#8997a4;text-decoration:none;transition:.2s}nav a:hover{color:#00e6a8}.top-right{display:flex;align-items:center;gap:9px;color:#82909d;font-size:9px}.online-dot,.meta-dot{width:7px;height:7px;background:#00e6a8;border-radius:50%;display:inline-block;box-shadow:0 0 12px #00e6a8}.menu-toggle{display:none;background:none;border:0;color:#00e6a8}
  .hero{min-height:690px;position:relative;display:grid;grid-template-columns:1.1fr .9fr;align-items:center;gap:55px;padding-top:70px;padding-bottom:100px}.hero-grid{position:absolute;inset:0;pointer-events:none;background-image:linear-gradient(rgba(43,70,82,.12) 1px,transparent 1px),linear-gradient(90deg,rgba(43,70,82,.12) 1px,transparent 1px);background-size:55px 55px;mask-image:linear-gradient(90deg,black,transparent 90%);opacity:.38}
  .hero-copy,.hero-console{position:relative;z-index:1}.eyebrow{color:#00dba4;display:flex;align-items:center;gap:11px}.prompt-symbol{font-size:17px}.eyebrow-line{height:1px;width:35px;background:#236c5a}.hero-kicker{font-family:monospace;color:#8b9aa7;font-size:12px;letter-spacing:4px;margin:42px 0 12px}.hero h1{font-size:clamp(64px,8.5vw,112px);line-height:.86;letter-spacing:-7px;margin:0;font-weight:900}.hero h1>span{color:#00e6a8}.cursor{color:#00e6a8;animation:blink 1s steps(2,start) infinite}@keyframes blink{to{visibility:hidden}}
  .role-line{font-family:monospace;font-size:10px;letter-spacing:1.4px;color:#aebac5;margin-top:26px}.role-bracket{color:#00e6a8}.role-divider{color:#49606b;padding:0 6px}.hero-description{color:#93a1ad;line-height:1.8;font-size:14px;max-width:470px;margin:24px 0 28px}.hero-actions,.connect-actions{display:flex;gap:12px;flex-wrap:wrap}.button-primary,.button-ghost{display:inline-flex;align-items:center;justify-content:center;gap:10px;text-decoration:none;font-family:monospace;font-size:10px;letter-spacing:1px;padding:14px 17px;transition:.2s}.button-primary{background:#00e6a8;color:#04110e;font-weight:900;border:1px solid #00e6a8}.button-primary:hover{background:#9bffe1;box-shadow:0 0 25px #00e6a833}.button-ghost{color:#c4d0d9;border:1px solid #2a3b46}.button-ghost:hover{border-color:#00e6a8;color:#00e6a8}.hero-meta{display:flex;gap:25px;color:#647480;font-size:9px;margin-top:44px}.hero-meta span:first-child{display:flex;align-items:center;gap:9px}.meta-dot{width:6px;height:6px}
  .hero-console,.terminal-window{border:1px solid #263a43;background:rgba(9,18,25,.92);box-shadow:0 20px 80px #0006,0 0 55px #00e6a80a}.console-top,.terminal-title{height:43px;border-bottom:1px solid #20313a;display:flex;align-items:center;padding:0 16px;gap:12px;color:#71818c;font:10px monospace}.window-dots{display:flex;gap:5px}.window-dots i{width:7px;height:7px;border-radius:50%;background:#38515b}.window-dots i:first-child{background:#ff6e68}.window-dots i:nth-child(2){background:#e9c45d}.window-dots i:nth-child(3){background:#00d69d}.console-live{margin-left:auto;color:#00e6a8;font-size:9px}.console-body{padding:25px 24px 16px;font:11px/1.85 "SFMono-Regular",Consolas,monospace;min-height:350px}.console-muted{color:#60717d;margin:0}.console-gap{height:24px}.console-green{color:#00e6a8}.console-blue{color:#63b8ff}.console-output{color:#c5d1d9;margin:2px 0 17px 0;padding-left:4px}.console-status{display:flex;justify-content:space-between;border-top:1px solid #20313a;margin-top:30px;padding-top:13px;color:#657985;font-size:9px}.console-status span:first-child{display:flex;align-items:center;gap:8px}.console-cursor{color:#00e6a8;animation:blink 1s steps(2,start) infinite}.scroll-cue{position:absolute;bottom:28px;left:0;display:flex;align-items:center;gap:12px;text-decoration:none;color:#647480;font:9px monospace;letter-spacing:1.5px}.scroll-cue svg{color:#00e6a8}
  .about-section,.focus-section,.projects-section,.skills-section,.terminal-section,.coding-section{padding-top:88px}.section-heading{display:flex;align-items:center;gap:17px;margin-bottom:34px}.section-index{color:#00e6a8;font-size:10px}.section-heading h2{font-size:22px;letter-spacing:1px;margin:0;font-weight:850;white-space:nowrap}.section-heading h2 span{color:#00e6a8}.heading-rule{height:1px;background:#253741;flex:1}.section-tag{color:#647580;font-size:9px;white-space:nowrap}.about-grid{display:grid;grid-template-columns:1.2fr .8fr;gap:70px;align-items:center}.section-label{color:#00c996;display:flex;align-items:center;gap:10px;font-size:9px}.tiny-square{width:7px;height:7px;background:#00e6a8;display:inline-block}.about-main h3{font-size:clamp(32px,4vw,48px);line-height:1.08;letter-spacing:-1.7px;margin:24px 0}.about-main h3 span{color:#00e6a8}.about-main>p{font-size:13px;line-height:1.9;color:#94a3ae;max-width:610px}.about-main strong{color:#dce7ed}.about-tags{display:flex;gap:9px;flex-wrap:wrap;margin-top:27px}.about-tags span,.stack-list span,.skill-items span{font:9px monospace;color:#91a2ad;border:1px solid #263942;padding:7px 9px}
  .info-panel{border:1px solid #263a43;background:#0a121a;padding:20px 22px}.panel-head{display:flex;justify-content:space-between;color:#00e6a8;border-bottom:1px solid #21323a;padding-bottom:16px}.panel-id{color:#5d707b}.info-row{display:flex;justify-content:space-between;gap:14px;padding:13px 0;border-bottom:1px solid #172832}.info-row>span{color:#687b86;font-size:9px}.info-row strong{font:11px monospace;color:#d3dfe6;text-align:right}.info-row .green-text{color:#00e6a8}.panel-foot{display:flex;justify-content:space-between;color:#71838d;font-size:9px;margin:20px 0 9px}.panel-foot span:last-child{color:#00e6a8}.progress-track{height:3px;background:#20333b}.progress-track span{display:block;width:100%;height:100%;background:#00e6a8;box-shadow:0 0 10px #00e6a8}
  .focus-section{padding-top:100px}.focus-grid{display:grid;grid-template-columns:repeat(3,1fr);border:1px solid #243741}.focus-card{display:flex;align-items:center;gap:15px;padding:22px 18px;border-right:1px solid #243741;color:#c3d0d8;font-size:12px;line-height:1.5}.focus-card:last-child{border:0}.focus-num{color:#00e6a8;font-size:11px}.focus-card svg{margin-left:auto;color:#526a75;flex-shrink:0}
  .projects-section{padding-top:105px}.section-intro{display:flex;justify-content:space-between;align-items:center;margin:-15px 0 22px}.section-intro p,.skills-note{font-size:12px;color:#81919c}.section-intro span{font:9px monospace;color:#00e6a8;letter-spacing:1px}.projects-grid{display:grid;grid-template-columns:repeat(2,1fr);gap:14px}.project-card{border:1px solid #263a43;background:linear-gradient(145deg,#0c151e,#091018);padding:22px 23px;position:relative;transition:.25s}.project-card:hover{border-color:#00a982;transform:translateY(-3px);box-shadow:0 14px 35px #0004}.project-top{display:flex;justify-content:space-between;color:#61737e;font-size:9px}.project-status{color:#00d69d;display:flex;align-items:center;gap:7px;font-size:8px}.project-status i{width:5px;height:5px;background:#00e6a8;border-radius:50%}.project-icon{width:43px;height:43px;border:1px solid #245448;background:#06231d;color:#00e6a8;display:grid;place-items:center;margin:25px 0 17px}.project-type{font-size:9px;color:#00b98b;margin:0 0 7px}.project-card h3{font-size:18px;margin:0 0 11px;letter-spacing:-.3px}.project-description{font-size:12px;line-height:1.7;color:#82929d;min-height:61px;margin:0 0 18px}.stack-list{display:flex;gap:6px;flex-wrap:wrap}.stack-list span{font-size:8px;padding:6px 8px}.project-link{display:flex;align-items:center;gap:7px;text-decoration:none;color:#00e6a8;margin-top:22px;font-size:9px}.project-link:hover{gap:12px}
  .skills-section{padding-top:105px}.skills-note{margin:-16px 0 22px}.skills-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px}.skill-card{border:1px solid #253943;background:#0a131b;padding:19px 17px;min-height:190px}.skill-card-head{display:flex;justify-content:space-between;align-items:center;color:#00e6a8}.skill-card-head span{font:8px monospace;color:#60727d}.skill-card h3{font:11px monospace;letter-spacing:1px;margin:23px 0 15px}.skill-items{display:flex;flex-wrap:wrap;gap:6px}.skill-items span{font-size:8px;padding:6px 7px}
  .terminal-section{padding-top:105px}.terminal-window{max-width:780px;margin:0 auto}.terminal-title{justify-content:flex-start}.terminal-title>span:nth-child(2){flex:1}.terminal-lock{color:#00e6a8;font-size:8px}.terminal-content{padding:23px 25px 19px;font:11px/1.8 monospace;min-height:225px}.terminal-welcome{color:#81919c;margin:0 0 20px}.terminal-prompt{color:#c2d0d8;margin:0 0 5px}.terminal-prompt span:first-child,.terminal-form>span{color:#00e6a8}.terminal-response{color:#8da0aa;white-space:pre-wrap;min-height:20px;margin:0 0 15px}.terminal-form{display:flex;align-items:center;gap:9px;border-top:1px solid #1e3039;padding-top:15px}.terminal-form input{flex:1;min-width:0;background:transparent;border:0;outline:0;color:#e3edf3;font:11px monospace}.terminal-form input::placeholder{color:#536671}.terminal-form button{background:transparent;border:1px solid #245446;color:#00e6a8;width:29px;height:27px;display:grid;place-items:center;cursor:pointer}.terminal-hint{font:9px monospace;color:#60727d;text-align:center;margin:14px 0 0}.terminal-hint .meta-dot{margin-right:8px}
  .coding-section{padding-top:105px}.coding-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:12px}.coding-card{display:flex;align-items:center;gap:14px;padding:18px;border:1px solid #253943;background:#0a131b;text-decoration:none;color:#e8edf5;transition:.2s}.coding-card:hover{border-color:#00a982;transform:translateY(-2px)}.coding-icon{width:42px;height:42px;display:grid;place-items:center;background:#241e13;color:#f0b84a;font:800 13px monospace;flex-shrink:0}.coding-icon.cf{background:#14222c;color:#70bfff}.coding-icon.cc{background:#211c28;color:#bd9bff}.coding-label{font-size:8px;color:#6e818d}.coding-card h3{font-size:14px;margin:4px 0}.coding-card p{font-size:10px;color:#758792;margin:0}.coding-card>svg{margin-left:auto;color:#00e6a8;flex-shrink:0}
  .connect-section{padding-top:110px;padding-bottom:90px}.connect-panel{position:relative;overflow:hidden;border:1px solid #245144;background:radial-gradient(ellipse at 80% 50%,#073126 0%,#0a171c 50%,#091119 100%);padding:55px 60px}.connect-orbit{position:absolute;border:1px solid #00e6a81b;border-radius:50%;width:390px;height:390px;right:-60px;top:-100px}.orbit-two{width:270px;height:270px;right:0;top:-40px}.connect-panel>*:not(.connect-orbit){position:relative;z-index:1}.connect-panel h2{font-size:clamp(43px,7vw,76px);letter-spacing:-3px;line-height:.95;margin:25px 0}.connect-panel h2 span{color:#00e6a8}.connect-panel>p{font-size:13px;line-height:1.8;color:#93a4ad;max-width:480px;margin-bottom:25px}
  .footer{min-height:85px;border-top:1px solid #1b2b34;display:flex;align-items:center;justify-content:space-between;gap:18px}.footer>p,.copyright{color:#63747e;font-size:8px}.footer>a:not(.brand){display:flex;align-items:center;gap:5px;color:#00e6a8;text-decoration:none;font-size:9px}
  @media(max-width:850px){.hero{gap:30px;grid-template-columns:1fr 1fr}.hero h1{font-size:75px}.role-line{font-size:8px}.about-grid{gap:30px}.skills-grid{grid-template-columns:repeat(2,1fr)}.top-right{display:none}}
  @media(max-width:650px){.section-wrap{width:calc(100% - 36px)}.topbar{height:64px;padding:0 18px}.menu-toggle{display:block;cursor:pointer}nav{display:none;position:absolute;top:63px;left:0;right:0;padding:22px;background:#091119;border-bottom:1px solid #263a43;flex-direction:column;gap:22px}nav.open{display:flex}.hero{display:flex;flex-direction:column;align-items:stretch;gap:38px;padding-top:75px;padding-bottom:85px;min-height:0}.hero h1{font-size:clamp(67px,17vw,100px);letter-spacing:-5px}.hero-kicker{margin-top:34px}.role-line{font-size:8px;line-height:1.9}.hero-meta{font-size:8px;gap:13px;flex-wrap:wrap}.hero-console{max-width:100%}.console-body{min-height:320px}.scroll-cue{bottom:25px}.section-heading{gap:9px}.section-heading h2{font-size:17px}.section-tag{display:none}.about-section,.focus-section,.projects-section,.skills-section,.terminal-section,.coding-section{padding-top:70px}.about-grid{grid-template-columns:1fr;gap:30px}.about-main h3{font-size:38px}.focus-grid{grid-template-columns:1fr}.focus-card{border-right:0;border-bottom:1px solid #243741}.focus-card:last-child{border-bottom:0}.projects-grid{grid-template-columns:1fr}.project-description{min-height:0}.skills-grid{grid-template-columns:1fr 1fr;gap:8px}.skill-card{padding:15px 12px;min-height:185px}.coding-grid{grid-template-columns:1fr}.connect-section{padding-top:75px;padding-bottom:60px}.connect-panel{padding:35px 23px}.connect-panel h2{font-size:48px;letter-spacing:-2px}.footer{padding:22px 0;flex-wrap:wrap}.footer>p{order:3;width:100%;margin:0}.copyright{font-size:7px}.section-intro{align-items:flex-start;gap:12px}.section-intro span{white-space:nowrap;font-size:8px}.terminal-content{padding:18px 14px}.terminal-form>span{font-size:9px}}
  @media(max-width:380px){.skills-grid{grid-template-columns:1fr}.hero-actions{flex-direction:column;align-items:stretch}.button-primary,.button-ghost{width:100%}.section-heading h2{font-size:15px}}
</style>