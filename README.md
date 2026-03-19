import { useState, useEffect, useRef, useCallback } from "react";
import {
  Terminal, Github, Linkedin, Mail, ExternalLink, Star,
  GitBranch, BarChart2, Database, TrendingUp, Award,
  ChevronRight, Cpu, Send, User, Code2, X
} from "lucide-react";

/* ─── CONSTANTS ─────────────────────────────────────────── */
const GH_USER = "sudipkumarjena";
const LI_URL  = "https://linkedin.com/in/sudip-kumar-jena515";
const EMAIL   = "sudip4185@gmail.com";

const SKILLS = [
  { name: "Power BI",  pct: 88, color: "#f59e0b", cat: "Visualization" },
  { name: "Excel",     pct: 85, color: "#10b981", cat: "Analysis" },
  { name: "SQL/MySQL", pct: 80, color: "#3b82f6", cat: "Database" },
  { name: "Python",    pct: 72, color: "#a78bfa", cat: "Programming" },
  { name: "Pandas",    pct: 70, color: "#a78bfa", cat: "Programming" },
  { name: "DAX",       pct: 78, color: "#f59e0b", cat: "Visualization" },
  { name: "Seaborn",   pct: 65, color: "#a78bfa", cat: "Programming" },
  { name: "Scikit-learn", pct: 50, color: "#a78bfa", cat: "ML" },
];

const CERTS = [
  { title: "GenAI Powered Data Analytics", issuer: "Tata × Forage",    date: "Dec 2025", color: "#f59e0b" },
  { title: "Data Analytics Job Simulation", issuer: "Deloitte × Forage", date: "Dec 2025", color: "#3b82f6" },
  { title: "Advanced Cert — Data Science & AI", issuer: "UpGrad",       date: "In Progress", color: "#10b981" },
  { title: "Digital Marketing",             issuer: "PW Skills",        date: "2025",        color: "#a78bfa" },
];

const PROJECTS = [
  {
    title: "Healthcare Patient Analysis",
    desc:  "End-to-end analysis of 54,966 patient records. Python cleaning → MySQL queries → 3-page Power BI dashboard with KPIs, billing trends & care quality metrics.",
    tags:  ["Python","MySQL","Power BI","DAX"],
    color: "#10b981",
    icon:  "🏥",
    link:  `https://github.com/${GH_USER}`,
  },
  {
    title: "Global AI Job Market Dashboard",
    desc:  "Analyzed 71,620 listings across 92 countries. Revealed 4,136% medium-company growth and $151K avg AI salary using interactive Power BI slicers.",
    tags:  ["Power BI","DAX","Data Modeling"],
    color: "#3b82f6",
    icon:  "🤖",
    link:  `https://github.com/${GH_USER}/AI-Job-Market-Analysis-PowerBI`,
  },
  {
    title: "Superstore Business Dashboard",
    desc:  "Sales analytics with regional profitability, segment intelligence, and seasonal trend analysis across 4 interactive report pages.",
    tags:  ["Power BI","Excel","Business Intelligence"],
    color: "#f59e0b",
    icon:  "🏪",
    link:  `https://github.com/${GH_USER}/superstore-sales-dashboard`,
  },
];

/* ─── ANIMATED COUNTER ──────────────────────────────────── */
function Counter({ end, duration = 1200 }) {
  const [val, setVal] = useState(0);
  const started = useRef(false);
  const ref = useRef(null);
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => {
      if (e.isIntersecting && !started.current) {
        started.current = true;
        const t0 = performance.now();
        const tick = (now) => {
          const p = Math.min((now - t0) / duration, 1);
          setVal(Math.floor(p * end));
          if (p < 1) requestAnimationFrame(tick);
        };
        requestAnimationFrame(tick);
      }
    });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, [end, duration]);
  return <span ref={ref}>{val}</span>;
}

/* ─── DATA PARTICLE BACKGROUND ──────────────────────────── */
function DataBackground() {
  const canvas = useRef(null);
  useEffect(() => {
    const c = canvas.current;
    const ctx = c.getContext("2d");
    const resize = () => { c.width = c.offsetWidth; c.height = c.offsetHeight; };
    resize();
    window.addEventListener("resize", resize);
    const pts = Array.from({ length: 60 }, () => ({
      x: Math.random() * c.width, y: Math.random() * c.height,
      vx: (Math.random() - 0.5) * 0.3, vy: (Math.random() - 0.5) * 0.3,
      r: Math.random() * 2 + 1,
      color: ["#f59e0b44","#3b82f644","#10b98144","#a78bfa44"][Math.floor(Math.random()*4)],
    }));
    let raf;
    const draw = () => {
      ctx.clearRect(0, 0, c.width, c.height);
      pts.forEach(p => {
        p.x += p.vx; p.y += p.vy;
        if (p.x < 0 || p.x > c.width) p.vx *= -1;
        if (p.y < 0 || p.y > c.height) p.vy *= -1;
        ctx.beginPath();
        ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
        ctx.fillStyle = p.color;
        ctx.fill();
      });
      pts.forEach((a, i) => pts.slice(i + 1).forEach(b => {
        const d = Math.hypot(a.x - b.x, a.y - b.y);
        if (d < 120) {
          ctx.beginPath();
          ctx.moveTo(a.x, a.y);
          ctx.lineTo(b.x, b.y);
          ctx.strokeStyle = `rgba(148,163,184,${0.08 * (1 - d / 120)})`;
          ctx.lineWidth = 0.5;
          ctx.stroke();
        }
      }));
      raf = requestAnimationFrame(draw);
    };
    draw();
    return () => { cancelAnimationFrame(raf); window.removeEventListener("resize", resize); };
  }, []);
  return <canvas ref={canvas} style={{ position:"absolute",inset:0,width:"100%",height:"100%",pointerEvents:"none" }} />;
}

/* ─── SKILL BAR ─────────────────────────────────────────── */
function SkillBar({ name, pct, color, delay = 0 }) {
  const [w, setW] = useState(0);
  const ref = useRef(null);
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => {
      if (e.isIntersecting) { setTimeout(() => setW(pct), delay); obs.disconnect(); }
    });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, [pct, delay]);
  return (
    <div ref={ref} style={{ marginBottom:14 }}>
      <div style={{ display:"flex", justifyContent:"space-between", marginBottom:5 }}>
        <span style={{ fontSize:13, color:"#94a3b8", fontFamily:"'Fira Code',monospace" }}>{name}</span>
        <span style={{ fontSize:12, color, fontFamily:"'Fira Code',monospace", fontWeight:600 }}>{pct}%</span>
      </div>
      <div style={{ background:"#1e293b", borderRadius:4, height:6, overflow:"hidden" }}>
        <div style={{
          width: `${w}%`, height:"100%", borderRadius:4,
          background: `linear-gradient(90deg, ${color}99, ${color})`,
          transition:"width 1.2s cubic-bezier(0.4,0,0.2,1)",
          boxShadow: `0 0 8px ${color}66`,
        }} />
      </div>
    </div>
  );
}

/* ─── AI TERMINAL ────────────────────────────────────────── */
function AITerminal() {
  const [msgs, setMsgs] = useState([
    { role:"assistant", content:"Hi! I'm Sudip's AI assistant. Ask me anything about his skills, projects, or experience. Try: 'What projects has Sudip built?' or 'What tools does he know?'" }
  ]);
  const [input, setInput] = useState("");
  const [loading, setLoading] = useState(false);
  const [open, setOpen] = useState(false);
  const bottomRef = useRef(null);
  useEffect(() => { bottomRef.current?.scrollIntoView({ behavior:"smooth" }); }, [msgs]);

  const send = async () => {
    if (!input.trim() || loading) return;
    const userMsg = input.trim();
    setInput("");
    setMsgs(m => [...m, { role:"user", content: userMsg }]);
    setLoading(true);
    try {
      const res = await fetch("https://api.anthropic.com/v1/messages", {
        method:"POST",
        headers:{ "Content-Type":"application/json" },
        body: JSON.stringify({
          model:"claude-sonnet-4-20250514",
          max_tokens:1000,
          system:`You are a concise, friendly AI portfolio assistant for Sudip Kumar Jena — an aspiring Data Analyst from Bhubaneswar, Odisha, India. Answer questions about him in 2-4 sentences max. Keep it professional and enthusiastic.

Key facts:
- B.Com graduate (Sambalpur University, 2025)
- Studying Advanced Data Science & AI at UpGrad
- Skills: Python, Pandas, NumPy, SQL, MySQL, Power BI, Excel, DAX, Matplotlib, Seaborn, Scikit-learn (basic ML)
- Projects: Healthcare Patient Analysis (54,966 records, Python+MySQL+Power BI), Global AI Job Market Dashboard (71,620 jobs, 92 countries), Superstore Business Dashboard
- Certifications: Tata GenAI Powered Data Analytics (Forage, Dec 2025), Deloitte Data Analytics (Forage, Dec 2025)
- Hackathon: UpGrad B-5 Squad — reached Pre-Final Round of Data & AI Hackathon 2026
- GitHub: github.com/sudipkumarjena
- LinkedIn: linkedin.com/in/sudip-kumar-jena515
- Open to: Data Analyst, Business Analyst, MIS Analyst roles`,
          messages: [...msgs, { role:"user", content: userMsg }].map(m => ({ role:m.role, content:m.content }))
        })
      });
      const data = await res.json();
      const text = data.content?.[0]?.text || "Sorry, I couldn't fetch a response.";
      setMsgs(m => [...m, { role:"assistant", content: text }]);
    } catch {
      setMsgs(m => [...m, { role:"assistant", content:"Connection error. Please try again." }]);
    }
    setLoading(false);
  };

  if (!open) return (
    <button onClick={() => setOpen(true)} style={{
      position:"fixed", bottom:28, right:28, zIndex:100,
      background:"linear-gradient(135deg,#f59e0b,#d97706)",
      border:"none", borderRadius:"50%", width:58, height:58,
      display:"flex", alignItems:"center", justifyContent:"center",
      cursor:"pointer", boxShadow:"0 0 24px #f59e0b66",
      animation:"pulse-btn 2s infinite"
    }}>
      <Cpu size={26} color="#0a0e1a" />
    </button>
  );

  return (
    <div style={{
      position:"fixed", bottom:28, right:28, zIndex:100,
      width:360, background:"#0f172a",
      border:"1px solid #f59e0b33", borderRadius:16,
      boxShadow:"0 0 40px #f59e0b22, 0 20px 60px #00000088",
      display:"flex", flexDirection:"column", overflow:"hidden",
    }}>
      <div style={{ background:"#1e293b", padding:"12px 16px", display:"flex", alignItems:"center", justifyContent:"space-between" }}>
        <div style={{ display:"flex", alignItems:"center", gap:8 }}>
          <div style={{ width:8, height:8, borderRadius:"50%", background:"#10b981", boxShadow:"0 0 6px #10b981" }} />
          <span style={{ fontFamily:"'Fira Code',monospace", fontSize:13, color:"#94a3b8" }}>sudip.ai_assistant</span>
        </div>
        <button onClick={() => setOpen(false)} style={{ background:"none", border:"none", cursor:"pointer", color:"#64748b" }}>
          <X size={16} />
        </button>
      </div>
      <div style={{ height:300, overflowY:"auto", padding:"16px", display:"flex", flexDirection:"column", gap:12 }}>
        {msgs.map((m, i) => (
          <div key={i} style={{
            alignSelf: m.role === "user" ? "flex-end" : "flex-start",
            maxWidth:"85%",
            background: m.role === "user" ? "linear-gradient(135deg,#f59e0b,#d97706)" : "#1e293b",
            color: m.role === "user" ? "#0a0e1a" : "#cbd5e1",
            padding:"10px 14px", borderRadius:12,
            fontSize:13, lineHeight:1.6,
            border: m.role === "assistant" ? "1px solid #334155" : "none",
          }}>
            {m.role === "assistant" && <span style={{ color:"#f59e0b", fontFamily:"'Fira Code',monospace", fontSize:11, display:"block", marginBottom:4 }}>⚡ AI</span>}
            {m.content}
          </div>
        ))}
        {loading && (
          <div style={{ alignSelf:"flex-start", background:"#1e293b", border:"1px solid #334155", padding:"10px 14px", borderRadius:12 }}>
            <span style={{ color:"#f59e0b", fontFamily:"'Fira Code',monospace", fontSize:11, display:"block", marginBottom:4 }}>⚡ AI</span>
            <span style={{ color:"#64748b", fontSize:13 }}>thinking...</span>
          </div>
        )}
        <div ref={bottomRef} />
      </div>
      <div style={{ padding:"12px 16px", borderTop:"1px solid #1e293b", display:"flex", gap:8 }}>
        <input
          value={input}
          onChange={e => setInput(e.target.value)}
          onKeyDown={e => e.key === "Enter" && send()}
          placeholder="Ask about Sudip..."
          style={{
            flex:1, background:"#1e293b", border:"1px solid #334155",
            borderRadius:8, padding:"8px 12px", color:"#e2e8f0",
            fontFamily:"'Fira Code',monospace", fontSize:13, outline:"none",
          }}
        />
        <button onClick={send} disabled={loading} style={{
          background: loading ? "#334155" : "linear-gradient(135deg,#f59e0b,#d97706)",
          border:"none", borderRadius:8, padding:"8px 12px",
          cursor: loading ? "not-allowed" : "pointer",
        }}>
          <Send size={16} color={loading ? "#64748b" : "#0a0e1a"} />
        </button>
      </div>
    </div>
  );
}

/* ─── PROJECT CARD ───────────────────────────────────────── */
function ProjectCard({ p, i }) {
  const [hov, setHov] = useState(false);
  return (
    <a href={p.link} target="_blank" rel="noreferrer"
      onMouseEnter={() => setHov(true)} onMouseLeave={() => setHov(false)}
      style={{
        display:"block", background:"#0f172a",
        border:`1px solid ${hov ? p.color+"66" : "#1e293b"}`,
        borderRadius:16, padding:24, textDecoration:"none",
        transition:"all 0.3s ease",
        transform: hov ? "translateY(-6px)" : "translateY(0)",
        boxShadow: hov ? `0 16px 40px ${p.color}22` : "0 2px 12px #00000033",
      }}>
      <div style={{ fontSize:32, marginBottom:12 }}>{p.icon}</div>
      <h3 style={{ color:"#f1f5f9", fontSize:17, fontWeight:700, marginBottom:10, fontFamily:"'Syne',sans-serif" }}>{p.title}</h3>
      <p style={{ color:"#64748b", fontSize:13, lineHeight:1.7, marginBottom:16 }}>{p.desc}</p>
      <div style={{ display:"flex", flexWrap:"wrap", gap:6, marginBottom:16 }}>
        {p.tags.map(t => (
          <span key={t} style={{
            background:`${p.color}18`, color:p.color,
            padding:"3px 10px", borderRadius:20, fontSize:11,
            fontFamily:"'Fira Code',monospace",
            border:`1px solid ${p.color}33`,
          }}>{t}</span>
        ))}
      </div>
      <div style={{ display:"flex", alignItems:"center", gap:6, color: hov ? p.color : "#475569", fontSize:13, transition:"color 0.3s" }}>
        <ExternalLink size={14}/><span style={{ fontFamily:"'Fira Code',monospace" }}>View on GitHub</span>
      </div>
    </a>
  );
}

/* ─── CERT CARD ──────────────────────────────────────────── */
function CertCard({ c }) {
  return (
    <div style={{
      background:"#0f172a", border:`1px solid ${c.color}33`,
      borderRadius:12, padding:"18px 20px",
      borderLeft:`3px solid ${c.color}`,
    }}>
      <div style={{ display:"flex", alignItems:"flex-start", justifyContent:"space-between", gap:8 }}>
        <div>
          <p style={{ color:"#f1f5f9", fontSize:14, fontWeight:600, marginBottom:4 }}>{c.title}</p>
          <p style={{ color:"#64748b", fontSize:12 }}>{c.issuer}</p>
        </div>
        <span style={{
          background:`${c.color}18`, color:c.color, fontSize:11,
          padding:"3px 10px", borderRadius:20, whiteSpace:"nowrap",
          fontFamily:"'Fira Code',monospace", flexShrink:0,
          border:`1px solid ${c.color}33`,
        }}>{c.date}</span>
      </div>
    </div>
  );
}

/* ─── MAIN APP ───────────────────────────────────────────── */
export default function App() {
  const [gh, setGh] = useState(null);
  const [activeSkillCat, setActiveSkillCat] = useState("All");

  useEffect(() => {
    fetch(`https://api.github.com/users/${GH_USER}`)
      .then(r => r.json()).then(setGh).catch(() => {});
  }, []);

  const cats = ["All", "Visualization", "Database", "Programming", "ML"];
  const filteredSkills = activeSkillCat === "All" ? SKILLS : SKILLS.filter(s => s.cat === activeSkillCat);

  const S = {
    page: { minHeight:"100vh", background:"#020817", color:"#cbd5e1", fontFamily:"'DM Sans',sans-serif", overflowX:"hidden" },
    nav: {
      position:"fixed", top:0, left:0, right:0, zIndex:50,
      background:"rgba(2,8,23,0.85)", backdropFilter:"blur(12px)",
      borderBottom:"1px solid #1e293b", padding:"0 32px",
      display:"flex", alignItems:"center", justifyContent:"space-between", height:64,
    },
    logo: { fontFamily:"'Fira Code',monospace", color:"#f59e0b", fontSize:18, fontWeight:700, letterSpacing:-0.5 },
    navLinks: { display:"flex", gap:32 },
    navLink: { color:"#64748b", fontSize:14, textDecoration:"none", transition:"color 0.2s", cursor:"pointer" },
    hero: {
      minHeight:"100vh", display:"flex", flexDirection:"column",
      alignItems:"center", justifyContent:"center",
      padding:"100px 24px 60px", textAlign:"center", position:"relative",
    },
    tag: {
      display:"inline-flex", alignItems:"center", gap:6,
      background:"#f59e0b18", border:"1px solid #f59e0b44",
      color:"#f59e0b", padding:"6px 16px", borderRadius:20,
      fontFamily:"'Fira Code',monospace", fontSize:12, marginBottom:24,
    },
    h1: { fontFamily:"'Syne',sans-serif", fontSize:"clamp(40px,7vw,80px)", fontWeight:800, color:"#f8fafc", lineHeight:1.05, letterSpacing:-2, marginBottom:20 },
    accent: { background:"linear-gradient(90deg,#f59e0b,#10b981)", WebkitBackgroundClip:"text", WebkitTextFillColor:"transparent" },
    subtitle: { color:"#64748b", fontSize:"clamp(15px,2vw,18px)", maxWidth:560, lineHeight:1.8, marginBottom:40 },
    statPill: {
      display:"flex", gap:32, background:"#0f172a",
      border:"1px solid #1e293b", borderRadius:16, padding:"20px 36px",
      marginBottom:36,
    },
    stat: { textAlign:"center" },
    statNum: { fontSize:28, fontWeight:800, fontFamily:"'Syne',sans-serif", color:"#f8fafc" },
    statLbl: { fontSize:12, color:"#475569", fontFamily:"'Fira Code',monospace", marginTop:2 },
    btnRow: { display:"flex", gap:12, flexWrap:"wrap", justifyContent:"center" },
    btnPrimary: {
      display:"inline-flex", alignItems:"center", gap:8,
      background:"linear-gradient(135deg,#f59e0b,#d97706)",
      color:"#0a0e1a", padding:"12px 28px", borderRadius:10,
      fontWeight:700, fontSize:14, textDecoration:"none", border:"none", cursor:"pointer",
    },
    btnOutline: {
      display:"inline-flex", alignItems:"center", gap:8,
      background:"transparent", color:"#94a3b8",
      border:"1px solid #334155", padding:"12px 24px", borderRadius:10,
      fontSize:14, textDecoration:"none", cursor:"pointer",
      transition:"all 0.2s",
    },
    section: { maxWidth:1100, margin:"0 auto", padding:"80px 24px" },
    sectionHead: { marginBottom:48 },
    sectionLabel: { fontFamily:"'Fira Code',monospace", fontSize:12, color:"#f59e0b", textTransform:"uppercase", letterSpacing:3, marginBottom:10 },
    sectionTitle: { fontFamily:"'Syne',sans-serif", fontSize:"clamp(28px,4vw,44px)", fontWeight:800, color:"#f8fafc", letterSpacing:-1 },
    grid3: { display:"grid", gridTemplateColumns:"repeat(auto-fill,minmax(300px,1fr))", gap:20 },
    grid2: { display:"grid", gridTemplateColumns:"repeat(auto-fill,minmax(280px,1fr))", gap:16 },
    filterRow: { display:"flex", gap:8, flexWrap:"wrap", marginBottom:32 },
    filterBtn: (active) => ({
      padding:"6px 18px", borderRadius:20, fontSize:13, cursor:"pointer",
      fontFamily:"'Fira Code',monospace", border:"1px solid",
      background: active ? "#f59e0b" : "transparent",
      color: active ? "#0a0e1a" : "#64748b",
      borderColor: active ? "#f59e0b" : "#334155",
      transition:"all 0.2s",
    }),
    divider: { maxWidth:1100, margin:"0 auto", borderTop:"1px solid #1e293b" },
    footer: {
      textAlign:"center", padding:"40px 24px",
      fontFamily:"'Fira Code',monospace", fontSize:13, color:"#334155",
    },
    socialRow: { display:"flex", gap:16, justifyContent:"center", marginBottom:20 },
    socialBtn: {
      display:"flex", alignItems:"center", justifyContent:"center",
      width:46, height:46, borderRadius:12, border:"1px solid #1e293b",
      background:"#0f172a", color:"#64748b",
      textDecoration:"none", transition:"all 0.2s",
    },
  };

  return (
    <div style={S.page}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Syne:wght@700;800&family=DM+Sans:wght@400;500&family=Fira+Code:wght@400;600&display=swap');
        * { box-sizing:border-box; margin:0; padding:0; }
        ::-webkit-scrollbar { width:6px; }
        ::-webkit-scrollbar-track { background:#020817; }
        ::-webkit-scrollbar-thumb { background:#1e293b; border-radius:4px; }
        @keyframes pulse-btn { 0%,100% { box-shadow:0 0 24px #f59e0b55; } 50% { box-shadow:0 0 40px #f59e0b99; } }
        @keyframes fadeUp { from { opacity:0; transform:translateY(24px); } to { opacity:1; transform:translateY(0); } }
        @keyframes float { 0%,100% { transform:translateY(0); } 50% { transform:translateY(-12px); } }
        .fade-up { animation: fadeUp 0.7s ease both; }
        .avatar-float { animation: float 4s ease-in-out infinite; }
        a:hover .social-icon { color: #f59e0b !important; border-color: #f59e0b44 !important; }
      `}</style>

      {/* NAV */}
      <nav style={S.nav}>
        <span style={S.logo}>⟨ sudip.data ⟩</span>
        <div style={S.navLinks}>
          {["About","Skills","Projects","Certs","Contact"].map(l => (
            <a key={l} href={`#${l.toLowerCase()}`} style={S.navLink}
              onMouseEnter={e => e.target.style.color="#f59e0b"}
              onMouseLeave={e => e.target.style.color="#64748b"}>
              {l}
            </a>
          ))}
        </div>
        <a href={`https://github.com/${GH_USER}`} target="_blank" rel="noreferrer"
          style={{ ...S.btnPrimary, padding:"8px 20px", fontSize:13 }}>
          <Github size={15}/> GitHub
        </a>
      </nav>

      {/* HERO */}
      <section id="about" style={S.hero}>
        <div style={{ position:"absolute",inset:0,overflow:"hidden" }}><DataBackground /></div>
        <div style={{ position:"relative", zIndex:2, display:"flex", flexDirection:"column", alignItems:"center" }}>
          {/* Avatar */}
          <div className="avatar-float" style={{
            width:110, height:110, borderRadius:"50%", overflow:"hidden",
            border:"3px solid #f59e0b44", marginBottom:28,
            boxShadow:"0 0 32px #f59e0b33",
            background:"#1e293b", display:"flex", alignItems:"center", justifyContent:"center",
          }}>
            {gh?.avatar_url
              ? <img src={gh.avatar_url} alt="Sudip" style={{ width:"100%", height:"100%", objectFit:"cover" }} />
              : <User size={48} color="#64748b" />
            }
          </div>

          <div style={S.tag}><BarChart2 size={13}/> Open to Data Analyst Roles</div>

          <h1 style={S.h1}>
            Sudip Kumar <span style={S.accent}>Jena</span>
          </h1>

          <p style={S.subtitle}>
            Aspiring Data Analyst turning raw datasets into business decisions —
            through Python, SQL, and Power BI dashboards that actually tell a story.
          </p>

          {/* Live stats */}
          {gh && (
            <div style={S.statPill} className="fade-up">
              {[
                { n: gh.public_repos, l:"Repos" },
                { n: gh.followers, l:"Followers" },
                { n: 54966, l:"Records Analysed", k:true },
              ].map(s => (
                <div key={s.l} style={S.stat}>
                  <div style={S.statNum}>
                    {s.k ? <><Counter end={s.n} />+</> : s.n}
                  </div>
                  <div style={S.statLbl}>{s.l}</div>
                </div>
              ))}
            </div>
          )}

          <div style={S.btnRow}>
            <a href={`https://github.com/${GH_USER}`} target="_blank" rel="noreferrer" style={S.btnPrimary}>
              <Github size={16}/> View GitHub
            </a>
            <a href={LI_URL} target="_blank" rel="noreferrer" style={S.btnOutline}>
              <Linkedin size={16}/> LinkedIn
            </a>
            <a href={`mailto:${EMAIL}`} style={S.btnOutline}>
              <Mail size={16}/> Email Me
            </a>
          </div>
        </div>
      </section>

      {/* SKILLS */}
      <section id="skills" style={{ background:"#060d1c" }}>
        <div style={S.section}>
          <div style={S.sectionHead}>
            <p style={S.sectionLabel}>// capabilities</p>
            <h2 style={S.sectionTitle}>Tech Stack</h2>
          </div>
          <div style={S.filterRow}>
            {cats.map(c => (
              <button key={c} onClick={() => setActiveSkillCat(c)} style={S.filterBtn(activeSkillCat === c)}>{c}</button>
            ))}
          </div>
          <div style={{ display:"grid", gridTemplateColumns:"repeat(auto-fill,minmax(340px,1fr))", gap:"0 48px" }}>
            {filteredSkills.map((s, i) => (
              <SkillBar key={s.name} {...s} delay={i * 80} />
            ))}
          </div>
        </div>
      </section>

      {/* PROJECTS */}
      <section id="projects" style={{ background:"#020817" }}>
        <div style={S.section}>
          <div style={S.sectionHead}>
            <p style={S.sectionLabel}>// portfolio</p>
            <h2 style={S.sectionTitle}>Featured Projects</h2>
          </div>
          <div style={S.grid3}>
            {PROJECTS.map((p, i) => <ProjectCard key={i} p={p} i={i} />)}
          </div>
          <div style={{ textAlign:"center", marginTop:40 }}>
            <a href={`https://github.com/${GH_USER}?tab=repositories`} target="_blank" rel="noreferrer"
              style={{ ...S.btnOutline, display:"inline-flex" }}
              onMouseEnter={e => { e.currentTarget.style.borderColor="#f59e0b"; e.currentTarget.style.color="#f59e0b"; }}
              onMouseLeave={e => { e.currentTarget.style.borderColor="#334155"; e.currentTarget.style.color="#94a3b8"; }}>
              All Repositories <ChevronRight size={16}/>
            </a>
          </div>
        </div>
      </section>

      {/* CERTS */}
      <section id="certs" style={{ background:"#060d1c" }}>
        <div style={S.section}>
          <div style={S.sectionHead}>
            <p style={S.sectionLabel}>// achievements</p>
            <h2 style={S.sectionTitle}>Certifications</h2>
          </div>
          <div style={S.grid2}>
            {CERTS.map((c, i) => <CertCard key={i} c={c} />)}
          </div>
          {/* Hackathon highlight */}
          <div style={{
            marginTop:20, background:"#0f172a",
            border:"1px solid #f59e0b44", borderRadius:12,
            padding:"20px 24px", display:"flex", alignItems:"center", gap:16,
            borderLeft:"3px solid #f59e0b",
          }}>
            <div style={{ fontSize:32 }}>🏆</div>
            <div>
              <p style={{ color:"#f8fafc", fontWeight:600, fontSize:15, marginBottom:4 }}>UpGrad B-5 Squad — Data & AI Hackathon 2026</p>
              <p style={{ color:"#64748b", fontSize:13 }}>Reached the Pre-Final Round competing with teams from across India in data analytics and AI problem-solving.</p>
            </div>
          </div>
        </div>
      </section>

      {/* CONTACT FOOTER */}
      <div style={S.divider} />
      <section id="contact" style={{ background:"#020817" }}>
        <div style={{ ...S.section, textAlign:"center" }}>
          <p style={S.sectionLabel}>// get in touch</p>
          <h2 style={{ ...S.sectionTitle, marginBottom:16 }}>Open to Opportunities</h2>
          <p style={{ color:"#64748b", fontSize:15, maxWidth:480, margin:"0 auto 40px", lineHeight:1.8 }}>
            Looking for Data Analyst, Business Analyst, or MIS Analyst roles. Let's talk data.
          </p>
          <div style={S.socialRow}>
            {[
              { href: `https://github.com/${GH_USER}`, icon:<Github size={20}/> },
              { href: LI_URL, icon:<Linkedin size={20}/> },
              { href: `mailto:${EMAIL}`, icon:<Mail size={20}/> },
            ].map((s, i) => (
              <a key={i} href={s.href} target="_blank" rel="noreferrer"
                style={S.socialBtn} className="social-icon"
                onMouseEnter={e => { e.currentTarget.style.color="#f59e0b"; e.currentTarget.style.borderColor="#f59e0b44"; e.currentTarget.style.background="#f59e0b11"; }}
                onMouseLeave={e => { e.currentTarget.style.color="#64748b"; e.currentTarget.style.borderColor="#1e293b"; e.currentTarget.style.background="#0f172a"; }}>
                {s.icon}
              </a>
            ))}
          </div>
        </div>
      </section>

      <footer style={S.footer}>
        <p>Built with React · © {new Date().getFullYear()} Sudip Kumar Jena · All systems operational.</p>
      </footer>

      {/* AI ASSISTANT */}
      <AITerminal />
    </div>
  );
}
