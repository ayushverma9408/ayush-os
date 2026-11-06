<script lang="ts">
  import { onMount } from 'svelte';
  import { Activity, ArrowDownRight, ArrowUpRight, ExternalLink, GitBranch, Github, RefreshCw, Star, Trophy, Users, Code2, Target } from 'lucide-svelte';

  const GITHUB_USER = 'ayushverma9408';
  const CODEFORCES_HANDLE = 'ayushverma9408';
  const LEETCODE_USER = 'ayushverma9408';
  const CACHE_TTL = 30 * 60 * 1000;

  type LoadState = 'loading' | 'ready' | 'error';
  type GitHubRepo = {
    name: string;
    html_url: string;
    description: string | null;
    stargazers_count: number;
    forks_count: number;
    language: string | null;
    updated_at: string;
  };
  type GitHubStats = {
    publicRepos: number;
    followers: number;
    totalStars: number;
    totalForks: number;
    repos: GitHubRepo[];
  };
  type CodeforcesStats = {
    rating: number;
    maxRating: number;
    rank: string;
    maxRank: string;
    contests: number;
    recentRatings: number[];
  };
  type LeetCodeStats = {
    totalSolved: number;
    easy: number;
    medium: number;
    hard: number;
    ranking: number;
    contestRating: number | null;
    contests: number;
  };

  let github = $state<GitHubStats | null>(null);
  let codeforces = $state<CodeforcesStats | null>(null);
  let leetcode = $state<LeetCodeStats | null>(null);
  let githubState = $state<LoadState>('loading');
  let codeforcesState = $state<LoadState>('loading');
  let leetcodeState = $state<LoadState>('loading');
  let lastUpdated = $state('');
  let refreshing = $state(false);

  function readCache<T>(key: string): T | null {
    try {
      const raw = localStorage.getItem(key);
      if (!raw) return null;
      const saved = JSON.parse(raw) as { at: number; data: T };
      return Date.now() - saved.at < CACHE_TTL ? saved.data : null;
    } catch {
      return null;
    }
  }

  function writeCache<T>(key: string, data: T) {
    try {
      localStorage.setItem(key, JSON.stringify({ at: Date.now(), data }));
    } catch {
      // Storage may be disabled or full; live data still works for this visit.
    }
  }

  async function getJson<T>(url: string): Promise<T> {
    const response = await fetch(url, { headers: { Accept: 'application/json' } });
    if (!response.ok) throw new Error(`Request failed (${response.status})`);
    return response.json() as Promise<T>;
  }

  async function loadGitHub(force = false) {
    githubState = 'loading';
    const cached = !force ? readCache<GitHubStats>('ayush-os:github') : null;
    if (cached) {
      github = cached;
      githubState = 'ready';
      return;
    }
    try {
      const profile = await getJson<{ public_repos: number; followers: number }>(
        `https://api.github.com/users/${GITHUB_USER}`
      );
      const repos = await getJson<GitHubRepo[]>(
        `https://api.github.com/users/${GITHUB_USER}/repos?per_page=100&sort=updated`
      );
      const result: GitHubStats = {
        publicRepos: profile.public_repos,
        followers: profile.followers,
        totalStars: repos.reduce((sum, repo) => sum + repo.stargazers_count, 0),
        totalForks: repos.reduce((sum, repo) => sum + repo.forks_count, 0),
        repos: [...repos].sort((a, b) => b.stargazers_count - a.stargazers_count).slice(0, 4)
      };
      github = result;
      writeCache('ayush-os:github', result);
      githubState = 'ready';
    } catch {
      githubState = 'error';
    }
  }

  async function loadCodeforces(force = false) {
    codeforcesState = 'loading';
    const cached = !force ? readCache<CodeforcesStats>('ayush-os:codeforces') : null;
    if (cached) {
      codeforces = cached;
      codeforcesState = 'ready';
      return;
    }
    try {
      const info = await getJson<{ status: string; result: Array<{ rating?: number; maxRating?: number; rank?: string; maxRank?: string }> }>(
        `https://codeforces.com/api/user.info?handles=${CODEFORCES_HANDLE}`
      );
      if (info.status !== 'OK' || !info.result?.[0]) throw new Error('Profile unavailable');

      // Codeforces asks API clients to keep requests spaced out.
      await new Promise((resolve) => setTimeout(resolve, 2100));
      const history = await getJson<{ status: string; result: Array<{ newRating: number }> }>(
        `https://codeforces.com/api/user.rating?handle=${CODEFORCES_HANDLE}`
      );
      if (history.status !== 'OK') throw new Error('Contest history unavailable');

      const user = info.result[0];
      const result: CodeforcesStats = {
        rating: user.rating ?? 0,
        maxRating: user.maxRating ?? user.rating ?? 0,
        rank: user.rank ?? 'Unrated',
        maxRank: user.maxRank ?? 'Unrated',
        contests: history.result.length,
        recentRatings: history.result.slice(-10).map((contest) => contest.newRating)
      };
      codeforces = result;
      writeCache('ayush-os:codeforces', result);
      codeforcesState = 'ready';
    } catch {
      codeforcesState = 'error';
    }
  }

  async function loadLeetCode(force = false) {
    leetcodeState = 'loading';
    const cached = !force ? readCache<LeetCodeStats>('ayush-os:leetcode') : null;
    if (cached) {
      leetcode = cached;
      leetcodeState = 'ready';
      return;
    }
    try {
      // Public profile query only. No login cookies, tokens, or private account data.
      const query = `query profile($username: String!) {
        matchedUser(username: $username) {
          profile { ranking }
          submitStats { acSubmissionNum { difficulty count } }
        }
        userContestRanking(username: $username) { rating attendedContestsCount }
      }`;
      const params = new URLSearchParams({
        query,
        variables: JSON.stringify({ username: LEETCODE_USER })
      });
      const response = await getJson<{
        data?: {
          matchedUser?: {
            profile?: { ranking?: number };
            submitStats?: { acSubmissionNum?: Array<{ difficulty: string; count: number }> };
          } | null;
          userContestRanking?: { rating?: number; attendedContestsCount?: number } | null;
        };
        errors?: Array<{ message: string }>;
      }>(`https://proxy.cors.dev/https://leetcode.com/graphql?${params.toString()}`);

      const user = response.data?.matchedUser;
      if (!user) throw new Error(response.errors?.[0]?.message ?? 'Profile unavailable');
      const counts = user.submitStats?.acSubmissionNum ?? [];
      const count = (difficulty: string) => counts.find((item) => item.difficulty === difficulty)?.count ?? 0;
      const result: LeetCodeStats = {
        totalSolved: count('All') || count('Easy') + count('Medium') + count('Hard'),
        easy: count('Easy'),
        medium: count('Medium'),
        hard: count('Hard'),
        ranking: user.profile?.ranking ?? 0,
        contestRating: response.data?.userContestRanking?.rating ?? null,
        contests: response.data?.userContestRanking?.attendedContestsCount ?? 0
      };
      leetcode = result;
      writeCache('ayush-os:leetcode', result);
      leetcodeState = 'ready';
    } catch {
      leetcodeState = 'error';
    }
  }

  async function refreshAll() {
    refreshing = true;
    await Promise.all([loadGitHub(true), loadCodeforces(true), loadLeetCode(true)]);
    lastUpdated = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
    refreshing = false;
  }

  function formatNumber(value: number) {
    return new Intl.NumberFormat('en-IN').format(value);
  }

  function relativeDate(value: string) {
    const days = Math.max(0, Math.floor((Date.now() - new Date(value).getTime()) / 86400000));
    if (days === 0) return 'today';
    if (days === 1) return '1 day ago';
    if (days < 30) return `${days} days ago`;
    return `${Math.floor(days / 30)} mo ago`;
  }

  onMount(() => {
    void Promise.all([loadGitHub(), loadCodeforces(), loadLeetCode()]);
    lastUpdated = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
  });
</script>

<section class="stats-module">
  <div class="module-heading">
    <div>
      <div class="module-kicker"><span></span> LIVE DATA STREAM</div>
      <h2>PLAYER <em>STATISTICS</em></h2>
      <p>Public profile metrics, fetched from the platforms themselves.</p>
    </div>
    <button class="refresh-button" onclick={refreshAll} disabled={refreshing}>
      <RefreshCw size={14} class={refreshing ? "spinning" : ""} /> {refreshing ? 'SYNCING' : 'SYNC DATA'}
    </button>
  </div>

  <div class="platform-grid">
    <article class="platform-card leetcode-card">
      <div class="platform-top"><div class="platform-brand"><span class="platform-monogram lc">LC</span><div><small>PROBLEM SOLVING</small><h3>LeetCode</h3></div></div><a href="https://leetcode.com/u/ayushverma9408/" target="_blank" rel="noreferrer" aria-label="Open LeetCode profile"><ExternalLink size={15}/></a></div>
      {#if leetcodeState === 'loading'}
        <div class="loading-state"><span class="loader"></span> FETCHING PROFILE</div>
      {:else if leetcodeState === 'error'}
        <div class="error-state">Stats temporarily unavailable. <a href="https://leetcode.com/u/ayushverma9408/" target="_blank" rel="noreferrer">Open profile <ArrowUpRight size={12}/></a></div>
      {:else if leetcode}
        <div class="main-metric"><strong>{formatNumber(leetcode.totalSolved)}</strong><span>PROBLEMS SOLVED</span></div>
        <div class="difficulty-bar"><span class="easy" style="width:{leetcode.totalSolved ? leetcode.easy / leetcode.totalSolved * 100 : 0}%"></span><span class="medium" style="width:{leetcode.totalSolved ? leetcode.medium / leetcode.totalSolved * 100 : 0}%"></span><span class="hard" style="width:{leetcode.totalSolved ? leetcode.hard / leetcode.totalSolved * 100 : 0}%"></span></div>
        <div class="difficulty-legend"><span><i class="easy-dot"></i>Easy <b>{leetcode.easy}</b></span><span><i class="medium-dot"></i>Med <b>{leetcode.medium}</b></span><span><i class="hard-dot"></i>Hard <b>{leetcode.hard}</b></span></div>
        <div class="platform-foot"><span>GLOBAL RANK <b>{leetcode.ranking ? '#' + formatNumber(leetcode.ranking) : '—'}</b></span><span>CONTESTS <b>{leetcode.contests}</b></span></div>
        {#if leetcode.contestRating}<div class="contest-rating">CONTEST RATING <strong>{Math.round(leetcode.contestRating)}</strong></div>{/if}
      {/if}
    </article>

    <article class="platform-card cf-card">
      <div class="platform-top"><div class="platform-brand"><span class="platform-monogram cf">CF</span><div><small>CONTEST ARENA</small><h3>Codeforces</h3></div></div><a href="https://codeforces.com/profile/ayushverma9408" target="_blank" rel="noreferrer" aria-label="Open Codeforces profile"><ExternalLink size={15}/></a></div>
      {#if codeforcesState === 'loading'}
        <div class="loading-state"><span class="loader"></span> FETCHING PROFILE</div>
      {:else if codeforcesState === 'error'}
        <div class="error-state">Stats temporarily unavailable. <a href="https://codeforces.com/profile/ayushverma9408" target="_blank" rel="noreferrer">Open profile <ArrowUpRight size={12}/></a></div>
      {:else if codeforces}
        <div class="rating-row"><div class="main-metric"><strong>{codeforces.rating || '—'}</strong><span>CURRENT RATING</span></div><div class="rank-pill">{codeforces.rank}</div></div>
        <div class="cf-chart" aria-label="Last ten contest ratings">
          {#each codeforces.recentRatings as rating, index}
            <div class="cf-bar-wrap" title={'Contest ' + (index + 1) + ': ' + rating}><div class="cf-bar" style="height:{Math.max(10, Math.min(100, rating / Math.max(1, codeforces.maxRating) * 100))}%"></div></div>
          {/each}
        </div>
        <div class="platform-foot"><span>MAX RATING <b>{codeforces.maxRating || '—'}</b></span><span>CONTESTS <b>{codeforces.contests}</b></span></div>
        <div class="contest-rating">HIGHEST TITLE <strong>{codeforces.maxRank}</strong></div>
      {/if}
    </article>
  </div>

  <article class="github-panel">
    <div class="github-panel-head">
      <div class="platform-brand"><span class="github-mark"><Github size={20}/></span><div><small>OPEN SOURCE ACTIVITY</small><h3>GitHub Overview</h3></div></div>
      <a href="https://github.com/ayushverma9408" target="_blank" rel="noreferrer">VIEW PROFILE <ArrowUpRight size={13}/></a>
    </div>
    {#if githubState === 'loading'}
      <div class="loading-state"><span class="loader"></span> FETCHING REPOSITORIES</div>
    {:else if githubState === 'error'}
      <div class="error-state">GitHub data temporarily unavailable. <a href="https://github.com/ayushverma9408" target="_blank" rel="noreferrer">Open profile <ArrowUpRight size={12}/></a></div>
    {:else if github}
      <div class="github-metrics">
        <div><span><GitBranch size={14}/> PUBLIC REPOSITORIES</span><strong>{github.publicRepos}</strong></div>
        <div><span><Star size={14}/> TOTAL STARS</span><strong>{github.totalStars}</strong></div>
        <div><span><GitBranch size={14}/> TOTAL FORKS</span><strong>{github.totalForks}</strong></div>
        <div><span><Users size={14}/> FOLLOWERS</span><strong>{github.followers}</strong></div>
      </div>
      <div class="repo-heading"><span>TOP PUBLIC REPOSITORIES</span><span>BY STARS</span></div>
      <div class="repo-list">
        {#each github.repos as repo}
          <a class="repo-row" href={repo.html_url} target="_blank" rel="noreferrer">
            <div class="repo-main"><strong>{repo.name}</strong><span>{repo.description || 'No description added yet.'}</span></div>
            <div class="repo-meta">{#if repo.language}<span class="repo-language"><i></i>{repo.language}</span>{/if}<span><Star size={12}/>{repo.stargazers_count}</span><span class="repo-updated">{relativeDate(repo.updated_at)}</span><ArrowUpRight size={13}/></div>
          </a>
        {/each}
      </div>
    {/if}
  </article>

  <div class="data-note"><Activity size={13}/><span>Stats are loaded from public profiles and cached in this browser for 30 minutes. External APIs can occasionally be unavailable.</span><span class="sync-time">LAST CHECK {lastUpdated || '—'}</span></div>
</section>

<style>
  .stats-module{margin-top:35px;border:1px solid #203640;background:linear-gradient(145deg,#0b151d,#091119);padding:25px}
  .module-heading{display:flex;justify-content:space-between;align-items:flex-end;gap:18px;margin-bottom:20px}
  .module-kicker{display:flex;align-items:center;gap:8px;color:#00d6a0;font:9px monospace;letter-spacing:1.4px}
  .module-kicker span{width:6px;height:6px;border-radius:50%;background:#00e6a8;box-shadow:0 0 10px #00e6a8}
  .module-heading h2{font-size:20px;letter-spacing:1px;margin:8px 0}.module-heading h2 em{font-style:normal;color:#00e6a8}
  .module-heading p{font-size:11px;color:#7f929d;margin:0}
  .refresh-button{display:flex;align-items:center;gap:8px;border:1px solid #285044;background:#0b201b;color:#00e6a8;padding:9px 11px;font:9px monospace;letter-spacing:.8px;white-space:nowrap;cursor:pointer}
  .refresh-button:disabled{opacity:.65;cursor:wait}.spinning{animation:spin 1s linear infinite}@keyframes spin{to{transform:rotate(360deg)}}
  .platform-grid{display:grid;grid-template-columns:1fr 1fr;gap:12px}
  .platform-card{border:1px solid #243943;background:#0b141c;padding:17px 18px;min-height:220px}
  .platform-top,.platform-brand{display:flex;align-items:center}.platform-top{justify-content:space-between}
  .platform-brand{gap:11px}.platform-brand>div{display:grid;gap:4px}.platform-brand small{font:8px monospace;color:#6c808c;letter-spacing:1px}.platform-brand h3{font-size:14px;margin:0}
  .platform-top>a{color:#728792}.platform-top>a:hover{color:#00e6a8}
  .platform-monogram,.github-mark{width:36px;height:36px;display:grid;place-items:center;font:800 11px monospace}
  .platform-monogram.lc{background:#292114;color:#f0b94e;border:1px solid #594525}.platform-monogram.cf{background:#142431;color:#7fc9ff;border:1px solid #2b4d63}.github-mark{color:#00e6a8;border:1px solid #285044;background:#0b211c}
  .main-metric{display:grid;gap:2px;margin-top:21px}.main-metric strong{font:800 29px Manrope,Inter,sans-serif;letter-spacing:-1px}.main-metric>span{font:8px monospace;color:#6d818c;letter-spacing:1px}
  .difficulty-bar{height:6px;border-radius:8px;overflow:hidden;display:flex;gap:2px;background:#1b2b33;margin-top:16px}.difficulty-bar span{height:100%;min-width:0}.easy{background:#00c995}.medium{background:#e9b653}.hard{background:#e16e78}
  .difficulty-legend{display:flex;gap:14px;margin-top:10px}.difficulty-legend span{display:flex;align-items:center;gap:5px;color:#84959f;font:9px monospace}.difficulty-legend b{color:#dce6eb;font-weight:600}.difficulty-legend i{width:6px;height:6px;border-radius:50%}.easy-dot{background:#00c995}.medium-dot{background:#e9b653}.hard-dot{background:#e16e78}
  .platform-foot{display:flex;justify-content:space-between;gap:10px;border-top:1px solid #20323a;padding-top:12px;margin-top:14px;color:#6f828d;font:8px monospace;letter-spacing:.5px}.platform-foot b{color:#c8d6dd;font-weight:600;margin-left:5px}
  .contest-rating{display:flex;justify-content:space-between;align-items:center;margin-top:12px;color:#71858f;font:8px monospace;letter-spacing:.7px}.contest-rating strong{color:#d7e4e9;font-size:10px}
  .rating-row{display:flex;justify-content:space-between;align-items:flex-end}.rank-pill{color:#85caff;background:#132534;border:1px solid #2c4b61;padding:5px 8px;font:9px monospace;text-transform:uppercase}
  .cf-chart{height:48px;display:flex;align-items:flex-end;gap:5px;margin-top:15px;padding-bottom:2px;border-bottom:1px solid #263943}.cf-bar-wrap{flex:1;height:100%;display:flex;align-items:flex-end}.cf-bar{width:100%;min-height:3px;background:linear-gradient(0deg,#087b65,#00e6a8);opacity:.8}
  .github-panel{border:1px solid #243943;background:#0b141c;margin-top:12px;padding:18px}
  .github-panel-head{display:flex;justify-content:space-between;align-items:center}.github-panel-head>a{display:flex;align-items:center;gap:5px;color:#00e6a8;text-decoration:none;font:8px monospace;letter-spacing:.7px}
  .github-metrics{display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin:19px 0 20px}
  .github-metrics>div{background:#0e1a22;border:1px solid #20343d;padding:11px 12px;display:grid;gap:8px}
  .github-metrics span{display:flex;align-items:center;gap:6px;color:#81949e;font:8px monospace;letter-spacing:.3px}.github-metrics span svg{color:#00c99a}
  .github-metrics strong{font:700 22px Manrope,Inter,sans-serif;color:#e6eff2}
  .repo-heading{display:flex;justify-content:space-between;color:#70848f;font:8px monospace;letter-spacing:1px;padding-bottom:8px;border-bottom:1px solid #20323a}
  .repo-heading span:last-child{color:#526a76}
  .repo-row{display:flex;align-items:center;justify-content:space-between;gap:12px;padding:11px 2px;border-bottom:1px solid #1a2b34;color:inherit;text-decoration:none}.repo-row:last-child{border-bottom:0}.repo-row:hover .repo-main strong{color:#00e6a8}
  .repo-main{display:grid;gap:4px;min-width:0}.repo-main strong{font:600 11px monospace;color:#cbd9df}.repo-main>span{font-size:9px;color:#71848e;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;max-width:440px}
  .repo-meta{display:flex;align-items:center;gap:11px;color:#82949d;font:8px monospace;flex-shrink:0}.repo-meta>span{display:flex;align-items:center;gap:4px}.repo-meta svg{color:#00c99a}.repo-language i{width:6px;height:6px;background:#00c99a;border-radius:50%}
  .data-note{display:flex;align-items:center;gap:8px;color:#657984;font:8px/1.5 monospace;margin-top:14px}.data-note>svg{color:#00c99a;flex-shrink:0}.sync-time{margin-left:auto;white-space:nowrap;color:#00c99a}
  .loading-state,.error-state{display:flex;align-items:center;gap:9px;color:#82949e;font:9px monospace;min-height:120px}.error-state{display:block;padding-top:35px;line-height:1.8}.error-state a{display:inline-flex;align-items:center;gap:4px;color:#00d6a0;text-decoration:none;margin-left:4px}
  .loader{width:12px;height:12px;border:1px solid #315147;border-top-color:#00e6a8;border-radius:50%;animation:spin .8s linear infinite}
  @media(max-width:650px){.stats-module{padding:17px;margin-top:25px}.module-heading{align-items:flex-start;flex-direction:column}.platform-grid{grid-template-columns:1fr}.github-metrics{grid-template-columns:repeat(2,1fr)}.repo-row{align-items:flex-start;flex-direction:column}.repo-main>span{max-width:100%;white-space:normal}.repo-meta{width:100%;justify-content:flex-start}.data-note{align-items:flex-start;flex-wrap:wrap}.sync-time{margin-left:21px}.platform-card{min-height:0}}
</style>
