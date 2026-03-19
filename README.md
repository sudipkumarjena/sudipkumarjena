import React, { useState, useEffect, useRef, useCallback } from 'react';
import { Terminal, Code, Cpu, Github, Linkedin, Mail, ChevronRight, ExternalLink, Star, GitBranch, FolderOpen, Users, BookOpen } from 'lucide-react';

// --- Custom Hooks ---
const useTypingEffect = (text, speed = 50, delay = 0) => {
  const [displayedText, setDisplayedText] = useState("");
  
  useEffect(() => {
    let timeout;
    let currentIndex = 0;
    setDisplayedText("");

  const startTyping = () => {
      const interval = setInterval(() => {
        if (currentIndex < text.length) {
          setDisplayedText(prev => prev + text.charAt(currentIndex));
          currentIndex++;
        } else {
          clearInterval(interval);
        }
      }, speed);
      return interval;
    };

  timeout = setTimeout(() => {
      const interval = startTyping();
      return () => clearInterval(interval);
    }, delay);

  return () => clearTimeout(timeout);
  }, [text, speed, delay]);

  return { displayedText };
};

// --- Components ---

// Matrix / Tech Particle Background
const MatrixBackground = () => {
  const canvasRef = useRef(null);

  useEffect(() => {
    const canvas = canvasRef.current;
    const ctx = canvas.getContext('2d');
    
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
    
  const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789$+-*/=%""\'#&_(),.;:?!\\|{}<>[]^~';
    const fontSize = 14;
    const columns = canvas.width / fontSize;
    const drops = Array.from({ length: columns }).fill(1);

   const draw = () => {
      ctx.fillStyle = 'rgba(2, 6, 23, 0.05)'; // Fade effect (Slate 950)
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      
   ctx.fillStyle = '#06b6d4'; // Cyan color
      ctx.font = fontSize + 'px monospace';
      
  for (let i = 0; i < drops.length; i++) {
        const text = letters.charAt(Math.floor(Math.random() * letters.length));
        ctx.fillText(text, i * fontSize, drops[i] * fontSize);
        
  if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) {
          drops[i] = 0;
        }
        drops[i]++;
      }
    };

  const interval = setInterval(draw, 33);
    
  const handleResize = () => {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    };
    
  window.addEventListener('resize', handleResize);
    return () => {
      clearInterval(interval);
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return <canvas ref={canvasRef} className="fixed inset-0 z-0 opacity-20 pointer-events-none" />;
};

// Glitch Text Component
const GlitchText = ({ text }) => {
  return (
    <h1 className="glitch-wrapper text-5xl md:text-7xl font-extrabold text-white mb-4 tracking-tight" data-text={text}>
      {text}
    </h1>
  );
};

// Advanced Terminal
const InteractiveTerminal = () => {
  const [history, setHistory] = useState([
    { cmd: 'system_init', output: 'Booting SudipOS kernel v2.4.1...' },
    { cmd: 'ls -la', output: 'drwxr-xr-x  about.txt\ndrwxr-xr-x  skills.json\ndrwxr-xr-x  projects/' }
  ]);
  const [input, setInput] = useState('');
  const endOfTerminalRef = useRef(null);

  const scrollToBottom = () => {
    endOfTerminalRef.current?.scrollIntoView({ behavior: "smooth" });
  };

  useEffect(() => scrollToBottom(), [history]);

  const handleCommand = (e) => {
    if (e.key === 'Enter' && input.trim() !== '') {
      let output = '';
      const cmd = input.trim().toLowerCase();
      
  switch(cmd) {
        case 'help': 
          output = 'Commands: whoami, ls, cat <file>, clear, hack, github'; 
          break;
        case 'whoami': 
          output = 'Sudip Kumar Jena - Engineer, Innovator, Code Weaver.'; 
          break;
        case 'ls':
        case 'ls -la':
          output = 'about.txt   skills.json   resume.pdf   projects/'; 
          break;
        case 'cat about.txt':
          output = 'Passionate developer building scalable apps and exploring cybernetics.';
          break;
        case 'cat skills.json':
          output = '{\n  "frontend": ["React", "Tailwind", "Next.js"],\n  "backend": ["Node", "Python", "SQL"],\n  "status": "Learning every day"\n}';
          break;
        case 'hack':
          output = 'Accessing mainframe... \nBypassing security protocols... \nAccess GRANTED. Just kidding, I am an ethical dev! 🚀';
          break;
        case 'clear': 
          setHistory([]); setInput(''); return;
        case 'github': 
          output = 'Redirecting to GitHub...'; 
          window.open('https://github.com/sudipkumarjena', '_blank'); 
          break;
        default: 
          if(cmd.startsWith('cat ')) output = `cat: ${cmd.substring(4)}: No such file or directory`;
          else if(cmd.startsWith('echo ')) output = cmd.substring(5);
          else output = `bash: ${cmd}: command not found`;
      }

  setHistory([...history, { cmd: input, output }]);
      setInput('');
    }
  };

  return (
    <div className="w-full max-w-2xl mx-auto bg-slate-900/90 backdrop-blur-xl rounded-xl overflow-hidden border border-slate-700 shadow-[0_0_40px_-10px_rgba(6,182,212,0.3)] mt-12 transition-transform hover:-translate-y-1 duration-300 relative z-20">
      <div className="bg-slate-950 px-4 py-3 flex items-center justify-between border-b border-slate-800">
        <div className="flex gap-2">
          <div className="w-3 h-3 rounded-full bg-red-500 hover:bg-red-400 transition-colors cursor-pointer"></div>
          <div className="w-3 h-3 rounded-full bg-yellow-500 hover:bg-yellow-400 transition-colors cursor-pointer"></div>
          <div className="w-3 h-3 rounded-full bg-green-500 hover:bg-green-400 transition-colors cursor-pointer"></div>
        </div>
        <span className="text-xs text-slate-500 font-mono">root@sudip-machine:~</span>
        <div className="w-12"></div> {/* Spacer for centering */}
      </div>
      <div className="p-5 font-mono text-sm h-72 overflow-y-auto text-slate-300 flex flex-col gap-3 custom-scrollbar">
        <div className="text-cyan-400 mb-2 font-bold">Type 'help' to see available commands.</div>
        {history.map((item, i) => (
          <div key={i} className="animate-fade-in">
            <div className="flex items-center text-emerald-400">
              <span className="mr-2 text-pink-500">λ</span>
              <span className="text-cyan-400 mr-2">~</span>
              <span className="text-white">{item.cmd}</span>
            </div>
            <pre className="text-slate-400 mt-1 whitespace-pre-wrap font-sans text-sm">{item.output}</pre>
          </div>
        ))}
        <div className="flex items-center text-emerald-400 mt-1">
          <span className="mr-2 text-pink-500 animate-pulse">λ</span>
          <span className="text-cyan-400 mr-2">~</span>
          <input 
            type="text" 
            value={input}
            onChange={(e) => setInput(e.target.value)}
            onKeyDown={handleCommand}
            className="bg-transparent border-none outline-none text-white flex-1 caret-cyan-500"
            autoFocus
            spellCheck="false"
          />
        </div>
        <div ref={endOfTerminalRef} />
      </div>
    </div>
  );
};

// 3D Tilt Project Card
const ProjectCard = ({ repo }) => {
  const cardRef = useRef(null);
  const [style, setStyle] = useState({});

  const handleMouseMove = useCallback((e) => {
    if (!cardRef.current) return;
    const rect = cardRef.current.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    
  const centerX = rect.width / 2;
    const centerY = rect.height / 2;
    
  const rotateX = ((y - centerY) / centerY) * -10;
    const rotateY = ((x - centerX) / centerX) * 10;

  setStyle({
      transform: `perspective(1000px) rotateX(${rotateX}deg) rotateY(${rotateY}deg) scale3d(1.02, 1.02, 1.02)`,
      transition: 'all 0.1s ease',
      zIndex: 10
    });
  }, []);

  const handleMouseLeave = useCallback(() => {
    setStyle({
     transform:`perspective(1000px) rotateX(0deg) rotateY(0deg) scale3d(1, 1, 1)`,
      transition: 'all 0.5s ease',
      zIndex: 1
    });
  }, []);

  return (
    <a 
      href={repo.html_url} 
      target="_blank" 
      rel="noreferrer"
      ref={cardRef}
      onMouseMove={handleMouseMove}
      onMouseLeave={handleMouseLeave}
      style={style}
      className="block relative bg-slate-900/60 backdrop-blur-md border border-slate-700 p-6 rounded-2xl cursor-pointer shadow-lg group"
    >
      {/* Dynamic lighting effect */}
      <div className="absolute inset-0 bg-gradient-to-br from-cyan-500/0 via-cyan-500/0 to-cyan-500/0 group-hover:from-cyan-500/10 group-hover:to-purple-500/10 transition-all duration-500 rounded-2xl pointer-events-none"></div>
      
  <div className="flex justify-between items-start mb-4 relative z-10">
        <div className="p-3 bg-slate-800 text-cyan-400 rounded-xl group-hover:bg-cyan-950 transition-colors">
          <FolderOpen size={24} />
        </div>
        <ExternalLink size={20} className="text-slate-500 group-hover:text-cyan-400 transition-colors" />
      </div>
      
  <h3 className="text-xl font-bold text-white mb-2 group-hover:text-cyan-300 transition-colors relative z-10 truncate">
        {repo.name}
      </h3>
      
  <p className="text-slate-400 mb-6 text-sm line-clamp-3 min-h-[60px] relative z-10">
        {repo.description || "No description provided. Dive into the code to see what this is about!"}
      </p>
      
  <div className="flex items-center justify-between text-xs font-mono mt-auto pt-4 border-t border-slate-800 relative z-10">
        <span className="flex items-center gap-1.5 text-slate-300">
          <div className={`w-2 h-2 rounded-full ${repo.language ? 'bg-cyan-400' : 'bg-slate-500'}`}></div>
          {repo.language || 'Markdown'}
        </span>
        <div className="flex gap-3 text-slate-400">
          <span className="flex items-center gap-1 hover:text-yellow-400 transition-colors"><Star size={14} /> {repo.stargazers_count}</span>
          <span className="flex items-center gap-1 hover:text-emerald-400 transition-colors"><GitBranch size={14} /> {repo.forks_count}</span>
        </div>
      </div>
    </a>
  );
};

// --- Main App ---
export default function App() {
  const [ghProfile, setGhProfile] = useState(null);
  const [ghRepos, setGhRepos] = useState([]);
  const [loading, setLoading] = useState(true);

  const { displayedText: subtitleText } = useTypingEffect("> INITIALIZING PROFILE... FETCHING LIVE GITHUB DATA_", 40, 500);

  // Fetch Live GitHub Data
  useEffect(() => {
    const fetchGitHubData = async () => {
      try {
        const username = 'sudipkumarjena';
        const [profileRes, reposRes] = await Promise.all([
          fetch(`https://api.github.com/users/${username}`),
          fetch(`https://api.github.com/users/${username}/repos?sort=updated&per_page=6`)
        ]);

  if (profileRes.ok && reposRes.ok) {
          const profileData = await profileRes.json();
          const reposData = await reposRes.json();
          setGhProfile(profileData);
          setGhRepos(reposData.filter(repo => !repo.fork)); // Filter out forks if preferred
        }
      } catch (error) {
        console.error("Error fetching GitHub data:", error);
      } finally {
        setLoading(false);
      }
    };

  fetchGitHubData();
  }, []);

  return (
    <div className="min-h-screen bg-slate-950 text-slate-200 font-sans selection:bg-cyan-500/30 selection:text-cyan-200 overflow-x-hidden relative">
      
  {/* Custom CSS for Glitch and Animations */}
      <style dangerouslySetInnerHTML={{__html: `
        /* Glitch Effect */
        .glitch-wrapper {
          position: relative;
        }
        .glitch-wrapper::before, .glitch-wrapper::after {
          content: attr(data-text);
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          opacity: 0.8;
        }
        .glitch-wrapper::before {
          left: 2px;
          text-shadow: -2px 0 #ff00c1;
          clip: rect(44px, 450px, 56px, 0);
          animation: glitch-anim 5s infinite linear alternate-reverse;
        }
        .glitch-wrapper::after {
          left: -2px;
          text-shadow: -2px 0 #00fff9;
          clip: rect(44px, 450px, 56px, 0);
          animation: glitch-anim2 5s infinite linear alternate-reverse;
        }
        @keyframes glitch-anim {
          0% { clip: rect(10px, 9999px, 86px, 0); }
          5% { clip: rect(61px, 9999px, 89px, 0); }
          10% { clip: rect(81px, 9999px, 26px, 0); }
          15% { clip: rect(20px, 9999px, 7px, 0); }
          20% { clip: rect(31px, 9999px, 42px, 0); }
          25% { clip: rect(4px, 9999px, 58px, 0); }
          30% { clip: rect(41px, 9999px, 67px, 0); }
          35% { clip: rect(82px, 9999px, 53px, 0); }
          40% { clip: rect(74px, 9999px, 78px, 0); }
          45% { clip: rect(86px, 9999px, 14px, 0); }
          50% { clip: rect(78px, 9999px, 75px, 0); }
          55% { clip: rect(13px, 9999px, 1px, 0); }
          60% { clip: rect(57px, 9999px, 83px, 0); }
          65% { clip: rect(67px, 9999px, 70px, 0); }
          70% { clip: rect(68px, 9999px, 3px, 0); }
          75% { clip: rect(80px, 9999px, 2px, 0); }
          80% { clip: rect(89px, 9999px, 63px, 0); }
          85% { clip: rect(98px, 9999px, 96px, 0); }
          90% { clip: rect(6px, 9999px, 22px, 0); }
          95% { clip: rect(70px, 9999px, 49px, 0); }
          100% { clip: rect(80px, 9999px, 61px, 0); }
        }
        @keyframes glitch-anim2 {
          0% { clip: rect(89px, 9999px, 31px, 0); }
          5% { clip: rect(68px, 9999px, 12px, 0); }
          10% { clip: rect(25px, 9999px, 80px, 0); }
          15% { clip: rect(71px, 9999px, 39px, 0); }
          20% { clip: rect(3px, 9999px, 54px, 0); }
          25% { clip: rect(48px, 9999px, 10px, 0); }
          30% { clip: rect(69px, 9999px, 63px, 0); }
          35% { clip: rect(4px, 9999px, 65px, 0); }
          40% { clip: rect(9px, 9999px, 59px, 0); }
          45% { clip: rect(21px, 9999px, 1px, 0); }
          50% { clip: rect(16px, 9999px, 94px, 0); }
          55% { clip: rect(33px, 9999px, 5px, 0); }
          60% { clip: rect(41px, 9999px, 29px, 0); }
          65% { clip: rect(66px, 9999px, 6px, 0); }
          70% { clip: rect(11px, 9999px, 47px, 0); }
          75% { clip: rect(85px, 9999px, 66px, 0); }
          80% { clip: rect(30px, 9999px, 68px, 0); }
          85% { clip: rect(79px, 9999px, 13px, 0); }
          90% { clip: rect(50px, 9999px, 41px, 0); }
          95% { clip: rect(56px, 9999px, 73px, 0); }
          100% { clip: rect(14px, 9999px, 82px, 0); }
        }
        
  /* Floating Avatar */
        @keyframes float-avatar {
          0%, 100% { transform: translateY(0) scale(1); box-shadow: 0 0 20px rgba(6, 182, 212, 0.2); }
          50% { transform: translateY(-15px) scale(1.02); box-shadow: 0 0 40px rgba(6, 182, 212, 0.5); }
        }
        .animate-float-avatar { animation: float-avatar 4s ease-in-out infinite; }
        
  /* Fade In */
        @keyframes fade-in { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .animate-fade-in { animation: fade-in 0.4s ease-out forwards; }
        
  .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #334155; border-radius: 10px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: #475569; }
      `}} />

  <MatrixBackground />

  {/* Navbar */}
      <nav className="fixed w-full top-0 z-40 bg-slate-950/80 backdrop-blur-md border-b border-slate-800/50 supports-[backdrop-filter]:bg-slate-950/40">
        <div className="max-w-6xl mx-auto px-6 py-4 flex justify-between items-center">
          <div className="text-cyan-400 font-mono font-bold text-xl flex items-center gap-2 tracking-tighter hover:text-white transition-colors cursor-pointer">
            <Terminal size={24} />
            <span>SUDIP.init()</span>
          </div>
          <a href="https://github.com/sudipkumarjena" target="_blank" rel="noreferrer" className="flex items-center gap-2 px-4 py-2 bg-slate-800/80 hover:bg-cyan-500 hover:text-slate-950 text-white rounded-lg border border-slate-700 hover:border-cyan-500 transition-all text-sm font-bold shadow-lg">
            <Github size={16} /> GitHub Profile
          </a>
        </div>
      </nav>

  {/* Main Content */}
      <main className="relative z-10 pt-32 pb-20 px-6 max-w-6xl mx-auto space-y-32">
        
  {/* Hero Section */}
        <section id="about" className="flex flex-col items-center text-center mt-10">
          <div className="relative inline-block mb-8 group">
            <div className="absolute -inset-1 bg-gradient-to-r from-cyan-500 to-purple-500 rounded-full blur opacity-30 group-hover:opacity-60 transition duration-1000 group-hover:duration-200"></div>
            <div className="w-32 h-32 md:w-44 md:h-44 rounded-full border-4 border-slate-800 overflow-hidden relative z-10 animate-float-avatar bg-slate-900 flex items-center justify-center p-1">
               {ghProfile?.avatar_url ? (
                 <img src={ghProfile.avatar_url} alt="Profile" className="w-full h-full rounded-full object-cover" />
               ) : (
                 <Cpu size={64} className="text-cyan-400 opacity-80" />
               )}
            </div>
            {/* Tech Badges */}
            <div className="absolute -top-4 -right-6 bg-slate-800/90 backdrop-blur px-3 py-1.5 rounded-lg border border-slate-700 animate-float-avatar text-cyan-400 z-20 shadow-xl text-xs font-mono font-bold" style={{ animationDelay: '1s' }}>{'<React />'}</div>
            <div className="absolute -bottom-2 -left-6 bg-slate-800/90 backdrop-blur px-3 py-1.5 rounded-lg border border-slate-700 animate-float-avatar text-yellow-400 z-20 shadow-xl text-xs font-mono font-bold" style={{ animationDelay: '2s' }}>{'{ JS }'}</div>
          </div>
          
   <GlitchText text={ghProfile?.name || "Sudip Kumar Jena"} />
          
  <p className="text-lg md:text-xl font-mono text-emerald-400 mb-8 h-8 max-w-2xl mx-auto">
            {ghProfile?.bio || subtitleText}
          </p>
          
  {/* Live GitHub Stats Grid */}
          {!loading && ghProfile && (
            <div className="flex gap-6 mb-12 animate-fade-in font-mono text-sm bg-slate-900/50 p-4 rounded-xl border border-slate-800 backdrop-blur-sm">
              <div className="flex flex-col items-center">
                <span className="text-slate-400 mb-1 flex items-center gap-1"><BookOpen size={14}/> Repos</span>
                <span className="text-xl text-white font-bold">{ghProfile.public_repos}</span>
              </div>
              <div className="w-px bg-slate-700"></div>
              <div className="flex flex-col items-center">
                <span className="text-slate-400 mb-1 flex items-center gap-1"><Users size={14}/> Followers</span>
                <span className="text-xl text-white font-bold">{ghProfile.followers}</span>
              </div>
              <div className="w-px bg-slate-700"></div>
              <div className="flex flex-col items-center">
                <span className="text-slate-400 mb-1 flex items-center gap-1"><GitBranch size={14}/> Following</span>
                <span className="text-xl text-white font-bold">{ghProfile.following}</span>
              </div>
            </div>
          )}

  <InteractiveTerminal />
        </section>

  {/* Live GitHub Projects Section */}
        <section id="projects" className="relative">
          <div className="flex items-center gap-4 mb-12">
            <h2 className="text-3xl font-bold text-white tracking-tight">System.Repositories</h2>
            <div className="h-px bg-gradient-to-r from-cyan-500/50 to-transparent flex-1"></div>
          </div>
          
  {loading ? (
             <div className="flex justify-center items-center py-20 text-cyan-500">
               <div className="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-cyan-500"></div>
             </div>
          ) : ghRepos.length > 0 ? (
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 perspective-1000">
              {ghRepos.map(repo => (
                <ProjectCard key={repo.id} repo={repo} />
              ))}
            </div>
          ) : (
            <div className="text-center p-12 bg-slate-900/50 border border-slate-800 rounded-xl">
              <Code size={48} className="mx-auto text-slate-600 mb-4" />
              <p className="text-slate-400">Loading repositories or no public repositories found.</p>
            </div>
          )}
          
  <div className="mt-16 text-center">
            <a href="https://github.com/sudipkumarjena?tab=repositories" target="_blank" rel="noreferrer" className="inline-flex items-center gap-2 px-8 py-3 bg-slate-900 border border-cyan-500/30 text-cyan-400 hover:bg-cyan-950 hover:border-cyan-400 rounded-full font-mono transition-all shadow-[0_0_20px_-5px_rgba(6,182,212,0.3)] hover:shadow-[0_0_30px_-5px_rgba(6,182,212,0.6)]">
              Explore All on GitHub <ChevronRight size={18} />
            </a>
          </div>
        </section>

  {/* Contact Footer */}
        <section id="contact" className="border-t border-slate-800/50 pt-20 pb-10 flex flex-col items-center text-center relative z-20">
          <h2 className="text-3xl font-bold text-white mb-6">Open Connection</h2>
          <p className="text-slate-400 max-w-md mb-8">
            My inbox is currently open for new opportunities, collaborations, or just a quick chat about tech.
          </p>
          
  <div className="flex gap-6 mb-12">
            <a href="https://github.com/sudipkumarjena" target="_blank" rel="noreferrer" className="p-4 bg-slate-900 rounded-xl text-slate-400 hover:text-white hover:-translate-y-2 hover:bg-slate-800 hover:shadow-[0_10px_20px_rgba(255,255,255,0.05)] transition-all border border-slate-800">
              <Github size={28} />
            </a>
            <a href="#" className="p-4 bg-slate-900 rounded-xl text-slate-400 hover:text-cyan-400 hover:-translate-y-2 hover:bg-slate-800 hover:shadow-[0_10px_20px_rgba(6,182,212,0.1)] transition-all border border-slate-800">
              <Linkedin size={28} />
            </a>
            <a href="mailto:hello@example.com" className="p-4 bg-slate-900 rounded-xl text-slate-400 hover:text-emerald-400 hover:-translate-y-2 hover:bg-slate-800 hover:shadow-[0_10px_20px_rgba(52,211,153,0.1)] transition-all border border-slate-800">
              <Mail size={28} />
            </a>
          </div>
          
   <div className="text-slate-600 font-mono text-sm">
            <p className="flex items-center justify-center gap-2">Built with <span className="text-cyan-600">React</span> & <span className="text-cyan-600">Tailwind</span></p>
            <p className="mt-2">© {new Date().getFullYear()} Sudip Kumar Jena. All systems normal.</p>
          </div>
        </section>
      </main>
    </div>
  );
}
