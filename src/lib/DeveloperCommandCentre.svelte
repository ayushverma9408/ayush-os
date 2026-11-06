<script lang="ts">
  import { onMount } from 'svelte';
  import { ArrowUpRight, Command, CornerDownLeft, Search, Terminal as TerminalIcon, X } from 'lucide-svelte';
  type Entry = { command: string; label: string; detail: string; output: string; target?: string; href?: string };
  const entries: Entry[] = [
    { command: 'help', label: 'Show commands', detail: 'List supported shell commands', output: 'Commands: help, about, projects, skills, stats, contact, whoami, clear, open github, open linkedin, open leetcode, open codeforces.' },
    { command: 'about', label: 'About Ayush', detail: 'Identity module', output: 'Computer Science undergraduate at NIT Silchar. Interested in algorithms, web development and learning by building.', target: 'about' },
    { command: 'projects', label: 'Project archive', detail: 'Explore projects and case studies', output: 'Opening project showcase.', target: 'projects' },
    { command: 'skills', label: 'Skill matrix', detail: 'Technologies and focus areas', output: 'Opening skill matrix.', target: 'skills' },
    { command: 'stats', label: 'Live statistics', detail: 'Coding and GitHub metrics', output: 'Opening live statistics.', target: 'live-stats' },
    { command: 'contact', label: 'Contact', detail: 'Ways to connect', output: 'Email: ayushverma19341@gmail.com · GitHub: @ayushverma9408', target: 'connect' },
    { command: 'whoami', label: 'Current user', detail: 'Print profile identity', output: 'guest@ayush-os · CSE undergraduate · NIT Silchar · Class of 2029' },
    { command: 'open github', label: 'GitHub profile', detail: 'Open GitHub in a new tab', output: 'Opening GitHub profile.', href: 'https://github.com/ayushverma9408' },
    { command: 'open linkedin', label: 'LinkedIn profile', detail: 'Open LinkedIn in a new tab', output: 'Opening LinkedIn profile.', href: 'https://www.linkedin.com/in/ayushverma9408/' },
    { command: 'open leetcode', label: 'LeetCode profile', detail: 'Open LeetCode in a new tab', output: 'Opening LeetCode profile.', href: 'https://leetcode.com/u/ayushverma9408/' },
    { command: 'open codeforces', label: 'Codeforces profile', detail: 'Open Codeforces in a new tab', output: 'Opening Codeforces profile.', href: 'https://codeforces.com/profile/ayushverma9408' }
  ];
  let input = $state('');
  let output = $state('AYUSH OS shell ready. Type help or press Ctrl K to open the command palette.');
  let history = $state<string[]>([]);
  let historyIndex = $state(-1);
  let paletteOpen = $state(false);
  let query = $state('');
  let selected = $state(0);
  let terminalLines = $state<{ command: string; result: string }[]>([]);
  const filtered = $derived(entries.filter((entry) => (entry.command + ' ' + entry.label + ' ' + entry.detail).toLowerCase().includes(query.toLowerCase())));
  function execute(raw: string) {
    const value = raw.trim().toLowerCase();
    if (!value) return;
    history = [...history, raw.trim()].slice(-40);
    historyIndex = -1;
    if (value === 'clear') { terminalLines = []; output = 'Terminal cleared.'; input = ''; return; }
    const entry = entries.find((item) => item.command === value);
    const result = entry?.output ?? 'Command not found: ' + value + '. Type help to see supported commands.';
    terminalLines = [...terminalLines, { command: raw.trim(), result }].slice(-12);
    output = result;
    input = '';
    if (entry?.target) document.getElementById(entry.target)?.scrollIntoView({ behavior: 'smooth', block: 'start' });
    if (entry?.href) window.open(entry.href, '_blank', 'noopener,noreferrer');
  }
  function choose(entry: Entry) { paletteOpen = false; query = ''; execute(entry.command); }
  function handleTerminalKey(event: KeyboardEvent) {
    if (event.key === 'ArrowUp' && history.length) { event.preventDefault(); historyIndex = historyIndex < 0 ? history.length - 1 : Math.max(0, historyIndex - 1); input = history[historyIndex] ?? ''; }
    else if (event.key === 'ArrowDown' && historyIndex >= 0) { event.preventDefault(); historyIndex = historyIndex >= history.length - 1 ? -1 : historyIndex + 1; input = historyIndex < 0 ? '' : history[historyIndex] ?? ''; }
  }
  function handlePaletteKey(event: KeyboardEvent) {
    if (event.key === 'ArrowDown') { event.preventDefault(); selected = Math.min(selected + 1, filtered.length - 1); }
    else if (event.key === 'ArrowUp') { event.preventDefault(); selected = Math.max(selected - 1, 0); }
    else if (event.key === 'Enter' && filtered[selected]) { event.preventDefault(); choose(filtered[selected]); }
  }
  onMount(() => {
    const onGlobalKey = (event: KeyboardEvent) => {
      if ((event.metaKey || event.ctrlKey) && event.key.toLowerCase() === 'k') { event.preventDefault(); paletteOpen = !paletteOpen; query = ''; selected = 0; }
      else if (event.key === 'Escape') paletteOpen = false;
    };
    window.addEventListener('keydown', onGlobalKey);
    return () => window.removeEventListener('keydown', onGlobalKey);
  });
</script>

<section class="section-wrap command-section" id="command-centre">
  <div class="section-heading"><span class="section-index">05 //</span><h2>DEVELOPER <span>COMMAND CENTRE</span></h2><span class="heading-rule"></span><span class="section-tag">SHELL.ACCESS</span></div>
  <div class="command-toolbar"><div><span class="live-dot"></span> SANDBOX ONLINE <span class="toolbar-divider">/</span> READ-ONLY PORTFOLIO COMMANDS</div><button class="palette-trigger" onclick={() => { paletteOpen = true; query = ''; selected = 0; }}><Command size={14}/> COMMAND PALETTE <kbd>CTRL K</kbd></button></div>
  <div class="terminal-window">
    <div class="terminal-title"><div class="window-dots"><i></i><i></i><i></i></div><span>guest@ayush-os — interactive shell</span><span class="terminal-lock">SAFE MODE</span></div>
    <div class="terminal-content">
      <p class="terminal-welcome">AYUSH OS [Version 2.0] · A safe shell for exploring this portfolio.</p>
      <div class="terminal-output" aria-live="polite">
        {#each terminalLines as line, i}<div class="terminal-entry"><p class="terminal-prompt"><span>guest@ayush-os</span>:~$ {line.command}</p><p class="terminal-response">{line.result}</p></div>{/each}
      </div>
      {#if terminalLines.length === 0}<p class="terminal-response initial-output">{output}</p>{/if}
      <form class="terminal-form" onsubmit={(event) => { event.preventDefault(); execute(input); }}>
        <span>guest@ayush-os:~$</span><input bind:value={input} onkeydown={handleTerminalKey} aria-label="Terminal command" placeholder="try help, projects, stats..." autocomplete="off" spellcheck="false"/><button type="submit" aria-label="Run command"><CornerDownLeft size={15}/></button>
      </form>
      <div class="terminal-footer"><span>↑ ↓ COMMAND HISTORY</span><span>CTRL K COMMAND PALETTE</span><span>{history.length} COMMANDS RUN</span></div>
    </div>
  </div>
  <p class="terminal-hint"><span class="live-dot"></span> SAFE SANDBOX · No operating-system commands are executed. Commands only navigate this portfolio or open public profile links.</p>
</section>

{#if paletteOpen}
  <div class="palette-backdrop" role="presentation" onclick={(event) => { if (event.target === event.currentTarget) paletteOpen = false; }}>
    <section class="command-palette" role="dialog" aria-modal="true" aria-label="Command palette">
      <div class="palette-search"><Search size={17}/><input bind:value={query} onkeydown={handlePaletteKey} placeholder="Search commands, sections, profiles..." aria-label="Search commands" autofocus/><kbd>ESC</kbd><button aria-label="Close command palette" onclick={() => paletteOpen = false}><X size={17}/></button></div>
      <div class="palette-label">SUGGESTED COMMANDS <span>{filtered.length} RESULTS</span></div>
      <div class="palette-results">
        {#each filtered as entry, i}<button class:active={selected === i} class="palette-item" onclick={() => choose(entry)} onmouseenter={() => selected = i}><span class="palette-icon"><TerminalIcon size={15}/></span><span class="palette-copy"><strong>{entry.label}</strong><small>{entry.detail}</small></span><code>{entry.command}</code><ArrowUpRight size={14}/></button>{/each}
        {#if filtered.length === 0}<p class="palette-empty">No matching command. Try projects, stats or contact.</p>{/if}
      </div>
      <div class="palette-foot"><span><kbd>↑</kbd><kbd>↓</kbd> NAVIGATE</span><span><kbd>ENTER</kbd> RUN COMMAND</span><span><kbd>ESC</kbd> CLOSE</span></div>
    </section>
  </div>
{/if}

<style>
  .command-section{padding-top:105px;scroll-margin-top:90px}.section-heading{display:flex;align-items:center;gap:17px;margin-bottom:34px}.section-index,.section-tag{font:10px monospace;letter-spacing:1.2px;color:#00e6a8}.section-heading h2{font-size:22px;letter-spacing:1px;margin:0;font-weight:850;white-space:nowrap}.section-heading h2 span{color:#00e6a8}.heading-rule{height:1px;background:#253741;flex:1}.section-tag{font-size:9px;color:#647580;white-space:nowrap}
  .command-toolbar{display:flex;align-items:center;justify-content:space-between;gap:14px;margin:-12px 0 13px;color:#738792;font:9px monospace;letter-spacing:.7px}.command-toolbar>div{display:flex;align-items:center;gap:9px}.live-dot{width:6px;height:6px;border-radius:50%;background:#00e6a8;box-shadow:0 0 10px #00e6a8;display:inline-block;flex-shrink:0}.toolbar-divider{color:#354c57}
  .palette-trigger{display:flex;align-items:center;gap:8px;padding:9px 11px;background:#0b1d1a;border:1px solid #285044;color:#00e6a8;font:9px monospace;letter-spacing:.6px;cursor:pointer}kbd{font:8px monospace;color:#9aabb4;background:#17262d;border:1px solid #2b414b;padding:3px 5px;border-radius:3px}
  .terminal-window{max-width:820px;margin:0 auto;border:1px solid #263a43;background:rgba(9,18,25,.92);box-shadow:0 20px 80px #0006,0 0 55px #00e6a80a}.terminal-title{height:43px;border-bottom:1px solid #20313a;display:flex;align-items:center;padding:0 16px;gap:12px;color:#71818c;font:10px monospace}.window-dots{display:flex;gap:5px}.window-dots i{width:7px;height:7px;border-radius:50%;background:#38515b}.window-dots i:first-child{background:#ff6e68}.window-dots i:nth-child(2){background:#e9c45d}.window-dots i:nth-child(3){background:#00d69d}.terminal-title>span:nth-child(2){flex:1}.terminal-lock{color:#00e6a8;font-size:8px}
  .terminal-content{padding:23px 25px 17px;font:11px/1.8 monospace;min-height:230px}.terminal-welcome{color:#81919c;margin:0 0 18px}.terminal-entry{margin-bottom:12px}.terminal-prompt{color:#c2d0d8;margin:0 0 2px}.terminal-prompt span{color:#00e6a8}.terminal-response{color:#8da0aa;white-space:pre-wrap;margin:0}.initial-output{margin:0 0 16px}
  .terminal-form{display:flex;align-items:center;gap:9px;border-top:1px solid #1e3039;padding-top:14px;margin-top:12px}.terminal-form>span{color:#00e6a8;white-space:nowrap}.terminal-form input{flex:1;min-width:0;background:transparent;border:0;outline:0;color:#e3edf3;font:11px monospace}.terminal-form input::placeholder{color:#536671}.terminal-form button{background:transparent;border:1px solid #245446;color:#00e6a8;width:29px;height:27px;display:grid;place-items:center;cursor:pointer}
  .terminal-footer{display:flex;justify-content:space-between;gap:10px;margin-top:12px;color:#526873;font:8px monospace;letter-spacing:.5px}.terminal-hint{font:9px monospace;color:#60727d;text-align:center;margin:14px 0 0}.terminal-hint .live-dot{margin-right:8px}
  .palette-backdrop{position:fixed;inset:0;z-index:100;background:rgba(1,5,9,.78);backdrop-filter:blur(9px);display:flex;justify-content:center;align-items:flex-start;padding:min(17vh,130px) 18px 30px}.command-palette{width:min(620px,100%);max-height:70vh;display:flex;flex-direction:column;background:#0b151d;border:1px solid #31504f;box-shadow:0 25px 100px #000b,0 0 45px #00e6a815}
  .palette-search{display:flex;align-items:center;gap:11px;padding:15px 17px;border-bottom:1px solid #243840;color:#00e6a8}.palette-search input{flex:1;min-width:0;background:transparent;border:0;outline:0;color:#e5edf2;font:12px monospace}.palette-search input::placeholder{color:#607580}.palette-search button{display:grid;place-items:center;border:0;background:transparent;color:#8da0aa;cursor:pointer}
  .palette-label{display:flex;justify-content:space-between;padding:13px 16px 8px;color:#6e838e;font:8px monospace;letter-spacing:1px}.palette-results{overflow:auto;padding:4px 8px 10px}.palette-item{width:100%;display:flex;align-items:center;gap:12px;padding:11px 10px;background:transparent;border:1px solid transparent;text-align:left;color:#dce7ec;cursor:pointer}.palette-item:hover,.palette-item.active{background:#10231f;border-color:#285044}
  .palette-icon{width:30px;height:30px;display:grid;place-items:center;color:#00e6a8;background:#0d2821;border:1px solid #244a3e;flex-shrink:0}.palette-copy{display:grid;gap:4px;flex:1;min-width:0}.palette-copy strong{font:11px monospace}.palette-copy small{font:9px monospace;color:#728791}.palette-item code{font:9px monospace;color:#82969f}.palette-item>svg{color:#00c995}.palette-empty{padding:20px;color:#7b8e98;text-align:center;font:10px monospace}.palette-foot{display:flex;justify-content:space-between;gap:8px;padding:11px 15px;border-top:1px solid #243840;color:#71848e;font:8px monospace}.palette-foot span{display:flex;align-items:center;gap:4px}
  @media(max-width:650px){.command-section{padding-top:75px}.section-heading{gap:9px}.section-heading h2{font-size:17px}.section-tag{display:none}.command-toolbar{align-items:flex-start;flex-direction:column}.terminal-content{padding:18px 14px}.terminal-footer{flex-wrap:wrap}.palette-backdrop{padding-top:10vh}.palette-item code{display:none}.palette-foot{flex-wrap:wrap;justify-content:flex-start}}
</style>