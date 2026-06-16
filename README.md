# Exam-Mitra
import { useState, useEffect } from "react";

// ─── MOCK DATA ───────────────────────────────────────────────────────────────
const ADMIN_CREDS = { email: "admin@examhub.in", password: "admin123" };

const initialExams = [
  {
    id: 1,
    name: "UPSC CSE",
    category: "Government",
    icon: "🏛️",
    description: "Union Public Service Commission Civil Services Examination",
    syllabus: "GS Paper I, II, III, IV + Optional Subject",
    examDate: "June 2025",
    totalSeats: 1056,
    featured: true,
  },
  {
    id: 2,
    name: "JEE Advanced",
    category: "Engineering",
    icon: "⚙️",
    description: "Joint Entrance Examination for IIT admissions",
    syllabus: "Physics, Chemistry, Mathematics",
    examDate: "May 2025",
    totalSeats: 17385,
    featured: true,
  },
  {
    id: 3,
    name: "NEET UG",
    category: "Medical",
    icon: "🏥",
    description: "National Eligibility cum Entrance Test for MBBS/BDS",
    syllabus: "Physics, Chemistry, Biology",
    examDate: "May 2025",
    totalSeats: 100000,
    featured: false,
  },
];

const initialQuestions = [
  {
    id: 1,
    examId: 1,
    year: 2023,
    subject: "History",
    question: "Consider the following statements regarding the Revolt of 1857...",
    options: ["Only 1", "Only 2", "Both 1 and 2", "Neither 1 nor 2"],
    answer: 2,
    explanation: "The Revolt of 1857 was a major uprising against British rule...",
    difficulty: "Medium",
    tags: ["History", "Modern India"],
  },
  {
    id: 2,
    examId: 2,
    year: 2023,
    subject: "Physics",
    question: "A particle moves in a circular path of radius r. If it completes one revolution in time T...",
    options: ["2πr/T", "πr/T", "4πr/T", "r/T"],
    answer: 0,
    explanation: "Speed = Distance/Time = 2πr/T (circumference/period)...",
    difficulty: "Easy",
    tags: ["Mechanics", "Circular Motion"],
  },
];

const initialCourses = [
  {
    id: 1,
    examId: 1,
    title: "UPSC Foundation Batch 2025",
    instructor: "Dr. Rajesh Kumar",
    duration: "12 months",
    price: 12999,
    originalPrice: 24999,
    enrolled: 4521,
    rating: 4.8,
    description: "Complete preparation course for UPSC CSE 2025 with live classes, notes and PYQs",
    topics: ["Polity", "History", "Geography", "Economics", "Current Affairs"],
    isFree: false,
  },
  {
    id: 2,
    examId: 2,
    title: "JEE Crash Course",
    instructor: "Prof. Ankit Sharma",
    duration: "3 months",
    price: 0,
    originalPrice: 5999,
    enrolled: 12300,
    rating: 4.6,
    description: "Free crash course covering all important topics for JEE Advanced",
    topics: ["Mechanics", "Thermodynamics", "Organic Chemistry", "Calculus"],
    isFree: true,
  },
];

// ─── ICONS ───────────────────────────────────────────────────────────────────
const Icon = ({ name, size = 18 }) => {
  const icons = {
    home: "🏠", exam: "📋", pyq: "❓", course: "📚", admin: "⚙️",
    logout: "🚪", add: "➕", edit: "✏️", delete: "🗑️", search: "🔍",
    star: "⭐", user: "👤", check: "✅", close: "✖️", arrow: "→",
    book: "📖", video: "🎥", pdf: "📄", info: "ℹ️", lock: "🔒",
    filter: "⚡", trending: "📈", time: "⏱️", save: "💾",
  };
  return <span style={{ fontSize: size }}>{icons[name] || "•"}</span>;
};

// ─── STYLES ──────────────────────────────────────────────────────────────────
const colors = {
  primary: "#5B4FE9",
  primaryDark: "#3D35B5",
  primaryLight: "#EEF0FF",
  accent: "#F59E0B",
  success: "#10B981",
  danger: "#EF4444",
  bg: "#F8F9FF",
  card: "#FFFFFF",
  text: "#1A1A2E",
  muted: "#64748B",
  border: "#E2E8F0",
  sidebar: "#1A1A2E",
  sidebarText: "#CBD5E1",
  sidebarActive: "#5B4FE9",
};

const S = {
  app: { display: "flex", height: "100vh", fontFamily: "'Segoe UI', system-ui, sans-serif", background: colors.bg, overflow: "hidden" },
  sidebar: { width: 240, background: colors.sidebar, display: "flex", flexDirection: "column", padding: "0", flexShrink: 0 },
  sidebarBrand: { padding: "20px 20px 16px", borderBottom: `1px solid #2D2D4E` },
  brandLogo: { fontSize: 22, fontWeight: 800, color: "#fff", letterSpacing: "-0.5px" },
  brandSub: { fontSize: 11, color: "#94A3B8", marginTop: 2 },
  sidebarNav: { flex: 1, padding: "12px 0", overflowY: "auto" },
  navSection: { padding: "8px 16px 4px", fontSize: 10, fontWeight: 700, color: "#475569", textTransform: "uppercase", letterSpacing: 1 },
  navItem: (active) => ({
    display: "flex", alignItems: "center", gap: 10, padding: "10px 20px",
    cursor: "pointer", fontSize: 14, fontWeight: active ? 600 : 400,
    color: active ? "#fff" : colors.sidebarText,
    background: active ? colors.sidebarActive : "transparent",
    borderRadius: active ? "0 20px 20px 0" : 0,
    marginRight: active ? 8 : 0,
    transition: "all 0.15s", borderLeft: active ? `3px solid ${colors.accent}` : "3px solid transparent",
  }),
  main: { flex: 1, display: "flex", flexDirection: "column", overflow: "hidden" },
  topbar: { background: "#fff", borderBottom: `1px solid ${colors.border}`, padding: "12px 24px", display: "flex", alignItems: "center", justifyContent: "space-between", flexShrink: 0 },
  content: { flex: 1, overflowY: "auto", padding: "24px" },
  card: { background: "#fff", borderRadius: 12, border: `1px solid ${colors.border}`, padding: 20, marginBottom: 16 },
  heading: { fontSize: 22, fontWeight: 700, color: colors.text, marginBottom: 4 },
  subheading: { fontSize: 14, color: colors.muted, marginBottom: 20 },
  grid: (cols = 3) => ({ display: "grid", gridTemplateColumns: `repeat(${cols}, 1fr)`, gap: 16 }),
  btn: (variant = "primary") => ({
    display: "inline-flex", alignItems: "center", gap: 6, padding: "8px 16px",
    borderRadius: 8, border: "none", cursor: "pointer", fontSize: 13, fontWeight: 600,
    background: variant === "primary" ? colors.primary : variant === "danger" ? colors.danger : variant === "success" ? colors.success : variant === "outline" ? "transparent" : "#F1F5F9",
    color: variant === "secondary" ? colors.text : "#fff",
    border: variant === "outline" ? `1px solid ${colors.border}` : "none",
  }),
  input: { width: "100%", padding: "9px 12px", borderRadius: 8, border: `1px solid ${colors.border}`, fontSize: 14, outline: "none", boxSizing: "border-box", background: "#fff" },
  label: { fontSize: 13, fontWeight: 600, color: colors.text, marginBottom: 5, display: "block" },
  badge: (color = colors.primary) => ({ display: "inline-block", padding: "2px 10px", borderRadius: 20, fontSize: 11, fontWeight: 700, background: color + "20", color }),
  statCard: { background: "#fff", borderRadius: 12, border: `1px solid ${colors.border}`, padding: "20px", textAlign: "center" },
  examCard: { background: "#fff", borderRadius: 12, border: `1px solid ${colors.border}`, padding: 20, cursor: "pointer", transition: "box-shadow 0.2s", position: "relative", overflow: "hidden" },
  modal: { position: "fixed", inset: 0, background: "rgba(0,0,0,0.5)", display: "flex", alignItems: "center", justifyContent: "center", zIndex: 1000, padding: 20 },
  modalBox: { background: "#fff", borderRadius: 16, padding: 28, width: "100%", maxWidth: 580, maxHeight: "85vh", overflowY: "auto" },
  tag: { display: "inline-block", padding: "2px 8px", borderRadius: 6, fontSize: 11, background: colors.primaryLight, color: colors.primary, marginRight: 4, marginBottom: 4 },
};

// ─── LOGIN PAGE ───────────────────────────────────────────────────────────────
function LoginPage({ onLogin }) {
  const [form, setForm] = useState({ email: "", password: "" });
  const [mode, setMode] = useState("student");
  const [error, setError] = useState("");

  const handleLogin = () => {
    if (mode === "admin") {
      if (form.email === ADMIN_CREDS.email && form.password === ADMIN_CREDS.password) {
        onLogin("admin");
      } else { setError("Invalid admin credentials"); }
    } else {
      if (form.email && form.password) { onLogin("student"); }
      else { setError("Please enter email and password"); }
    }
  };

  return (
    <div style={{ minHeight: "100vh", background: `linear-gradient(135deg, ${colors.primary} 0%, #3D35B5 50%, #1A1A2E 100%)`, display: "flex", alignItems: "center", justifyContent: "center", padding: 20 }}>
      <div style={{ background: "#fff", borderRadius: 20, padding: "40px 36px", width: "100%", maxWidth: 420, boxShadow: "0 25px 60px rgba(0,0,0,0.3)" }}>
        <div style={{ textAlign: "center", marginBottom: 28 }}>
          <div style={{ fontSize: 40, marginBottom: 8 }}>📚</div>
          <div style={{ fontSize: 26, fontWeight: 800, color: colors.text }}>ExamHub</div>
          <div style={{ fontSize: 13, color: colors.muted, marginTop: 4 }}>India's Premier Exam Preparation Platform</div>
        </div>

        <div style={{ display: "flex", background: colors.bg, borderRadius: 10, padding: 4, marginBottom: 24 }}>
          {["student", "admin"].map(m => (
            <button key={m} onClick={() => { setMode(m); setError(""); }}
              style={{ flex: 1, padding: "8px", borderRadius: 8, border: "none", cursor: "pointer", fontSize: 13, fontWeight: 600, background: mode === m ? colors.primary : "transparent", color: mode === m ? "#fff" : colors.muted, transition: "all 0.2s" }}>
              {m === "admin" ? "🔒 Admin" : "👤 Student"}
            </button>
          ))}
        </div>

        {["email", "password"].map(field => (
          <div key={field} style={{ marginBottom: 16 }}>
            <label style={S.label}>{field === "email" ? "Email / Phone" : "Password"}</label>
            <input type={field} placeholder={field === "email" ? (mode === "admin" ? "admin@examhub.in" : "your@email.com") : "Enter password"}
              value={form[field]} onChange={e => { setForm(p => ({ ...p, [field]: e.target.value })); setError(""); }}
              onKeyDown={e => e.key === "Enter" && handleLogin()}
              style={S.input} />
          </div>
        ))}

        {error && <div style={{ color: colors.danger, fontSize: 13, marginBottom: 12, padding: "8px 12px", background: "#FEF2F2", borderRadius: 8 }}>{error}</div>}

        <button onClick={handleLogin} style={{ ...S.btn("primary"), width: "100%", justifyContent: "center", padding: "12px", fontSize: 15 }}>
          {mode === "admin" ? "Login as Admin" : "Login / Sign Up"}
        </button>

        {mode === "admin" && (
          <div style={{ marginTop: 12, padding: "10px 14px", background: colors.primaryLight, borderRadius: 8, fontSize: 12, color: colors.primary }}>
            Demo: admin@examhub.in / admin123
          </div>
        )}
      </div>
    </div>
  );
}

// ─── MODAL ────────────────────────────────────────────────────────────────────
function Modal({ title, onClose, children }) {
  return (
    <div style={S.modal} onClick={e => e.target === e.currentTarget && onClose()}>
      <div style={S.modalBox}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 20 }}>
          <div style={{ fontSize: 18, fontWeight: 700, color: colors.text }}>{title}</div>
          <button onClick={onClose} style={{ background: "none", border: "none", fontSize: 20, cursor: "pointer", color: colors.muted }}>✖</button>
        </div>
        {children}
      </div>
    </div>
  );
}

// ─── DASHBOARD HOME ───────────────────────────────────────────────────────────
function Dashboard({ exams, questions, courses, role }) {
  const stats = [
    { label: "Total Exams", value: exams.length, icon: "📋", color: colors.primary },
    { label: "PYQ Questions", value: questions.length, icon: "❓", color: colors.accent },
    { label: "Courses", value: courses.length, icon: "📚", color: colors.success },
    { label: "Students", value: "12,450", icon: "👥", color: "#8B5CF6" },
  ];

  return (
    <div>
      <div style={S.heading}>Welcome to ExamHub {role === "admin" ? "Admin" : ""} 🎯</div>
      <div style={S.subheading}>Your complete exam preparation destination</div>

      <div style={S.grid(4)}>
        {stats.map(s => (
          <div key={s.label} style={{ ...S.statCard, borderTop: `4px solid ${s.color}` }}>
            <div style={{ fontSize: 32, marginBottom: 8 }}>{s.icon}</div>
            <div style={{ fontSize: 26, fontWeight: 800, color: s.color }}>{s.value}</div>
            <div style={{ fontSize: 13, color: colors.muted, marginTop: 4 }}>{s.label}</div>
          </div>
        ))}
      </div>

      <div style={{ marginTop: 8, ...S.card }}>
        <div style={{ fontSize: 16, fontWeight: 700, marginBottom: 16, color: colors.text }}>🔥 Featured Exams</div>
        <div style={S.grid(3)}>
          {exams.filter(e => e.featured).map(exam => (
            <div key={exam.id} style={{ padding: 16, borderRadius: 10, background: colors.primaryLight, border: `1px solid ${colors.primary}30` }}>
              <div style={{ fontSize: 28, marginBottom: 8 }}>{exam.icon}</div>
              <div style={{ fontWeight: 700, color: colors.text }}>{exam.name}</div>
              <div style={{ fontSize: 12, color: colors.muted, marginTop: 4 }}>{exam.description}</div>
              <div style={{ marginTop: 10, ...S.badge(colors.primary) }}>{exam.category}</div>
            </div>
          ))}
        </div>
      </div>

      <div style={S.card}>
        <div style={{ fontSize: 16, fontWeight: 700, marginBottom: 16, color: colors.text }}>📊 Recent PYQs Added</div>
        {questions.slice(0, 3).map(q => (
          <div key={q.id} style={{ padding: "12px 0", borderBottom: `1px solid ${colors.border}`, display: "flex", gap: 12, alignItems: "flex-start" }}>
            <div style={{ ...S.badge(q.difficulty === "Easy" ? colors.success : q.difficulty === "Hard" ? colors.danger : colors.accent), whiteSpace: "nowrap" }}>{q.difficulty}</div>
            <div>
              <div style={{ fontSize: 14, color: colors.text, fontWeight: 500 }}>{q.question.substring(0, 90)}...</div>
              <div style={{ fontSize: 12, color: colors.muted, marginTop: 4 }}>{exams.find(e => e.id === q.examId)?.name} • {q.year} • {q.subject}</div>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── EXAMS PAGE ───────────────────────────────────────────────────────────────
function ExamsPage({ exams, setExams, role, questions, courses }) {
  const [modal, setModal] = useState(null);
  const [form, setForm] = useState({});
  const [search, setSearch] = useState("");
  const [selected, setSelected] = useState(null);

  const filtered = exams.filter(e => e.name.toLowerCase().includes(search.toLowerCase()) || e.category.toLowerCase().includes(search.toLowerCase()));

  const openAdd = () => { setForm({ id: Date.now(), name: "", category: "", icon: "📋", description: "", syllabus: "", examDate: "", totalSeats: "", featured: false }); setModal("add"); };
  const openEdit = (exam) => { setForm({ ...exam }); setModal("edit"); };
  const saveExam = () => {
    if (modal === "add") setExams(p => [...p, form]);
    else setExams(p => p.map(e => e.id === form.id ? form : e));
    setModal(null);
  };
  const deleteExam = (id) => { if (confirm("Delete this exam?")) setExams(p => p.filter(e => e.id !== id)); };

  const FormField = ({ label, field, type = "text", placeholder }) => (
    <div style={{ marginBottom: 14 }}>
      <label style={S.label}>{label}</label>
      {type === "textarea"
        ? <textarea value={form[field] || ""} onChange={e => setForm(p => ({ ...p, [field]: e.target.value }))} placeholder={placeholder} rows={3} style={{ ...S.input, resize: "vertical" }} />
        : type === "checkbox"
          ? <label style={{ display: "flex", alignItems: "center", gap: 8, cursor: "pointer" }}>
              <input type="checkbox" checked={form[field] || false} onChange={e => setForm(p => ({ ...p, [field]: e.target.checked }))} />
              <span style={{ fontSize: 13, color: colors.muted }}>Featured on homepage</span>
            </label>
          : <input type={type} value={form[field] || ""} onChange={e => setForm(p => ({ ...p, [field]: e.target.value }))} placeholder={placeholder} style={S.input} />
      }
    </div>
  );

  if (selected) {
    const examQs = questions.filter(q => q.examId === selected.id);
    const examCourses = courses.filter(c => c.examId === selected.id);
    return (
      <div>
        <button onClick={() => setSelected(null)} style={{ ...S.btn("outline"), marginBottom: 16 }}>← Back to Exams</button>
        <div style={{ display: "flex", gap: 16, alignItems: "flex-start", marginBottom: 24 }}>
          <div style={{ fontSize: 56 }}>{selected.icon}</div>
          <div>
            <div style={S.heading}>{selected.name}</div>
            <div style={S.subheading}>{selected.description}</div>
            <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
              <span style={S.badge(colors.primary)}>{selected.category}</span>
              <span style={S.badge(colors.accent)}>Exam Date: {selected.examDate}</span>
              <span style={S.badge(colors.success)}>Seats: {selected.totalSeats?.toLocaleString()}</span>
            </div>
          </div>
        </div>
        <div style={S.grid(2)}>
          <div style={S.card}>
            <div style={{ fontWeight: 700, marginBottom: 10 }}>📋 Syllabus</div>
            <div style={{ fontSize: 14, color: colors.muted }}>{selected.syllabus}</div>
          </div>
          <div style={S.card}>
            <div style={{ fontWeight: 700, marginBottom: 10 }}>📊 Stats</div>
            <div style={{ fontSize: 14, color: colors.muted }}>PYQs: {examQs.length} questions</div>
            <div style={{ fontSize: 14, color: colors.muted, marginTop: 4 }}>Courses: {examCourses.length}</div>
          </div>
        </div>
        <div style={S.card}>
          <div style={{ fontWeight: 700, marginBottom: 14 }}>📚 Available Courses</div>
          {examCourses.length ? examCourses.map(c => (
            <div key={c.id} style={{ padding: "12px 0", borderBottom: `1px solid ${colors.border}` }}>
              <div style={{ fontWeight: 600 }}>{c.title}</div>
              <div style={{ fontSize: 13, color: colors.muted }}>{c.instructor} • {c.duration}</div>
              <div style={{ marginTop: 6, display: "flex", gap: 8, alignItems: "center" }}>
                {c.isFree ? <span style={S.badge(colors.success)}>FREE</span> : <span style={{ fontWeight: 700, color: colors.primary }}>₹{c.price.toLocaleString()}</span>}
                <span style={{ fontSize: 12, color: colors.muted }}>⭐ {c.rating} • {c.enrolled.toLocaleString()} enrolled</span>
              </div>
            </div>
          )) : <div style={{ color: colors.muted, fontSize: 14 }}>No courses yet</div>}
        </div>
      </div>
    );
  }

  return (
    <div>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 20 }}>
        <div>
          <div style={S.heading}>All Exams</div>
          <div style={S.subheading}>{filtered.length} exams available</div>
        </div>
        {role === "admin" && <button onClick={openAdd} style={S.btn("primary")}><Icon name="add" /> Add Exam</button>}
      </div>

      <div style={{ position: "relative", marginBottom: 20 }}>
        <span style={{ position: "absolute", left: 12, top: "50%", transform: "translateY(-50%)", fontSize: 16 }}>🔍</span>
        <input placeholder="Search exams..." value={search} onChange={e => setSearch(e.target.value)} style={{ ...S.input, paddingLeft: 38 }} />
      </div>

      <div style={S.grid(3)}>
        {filtered.map(exam => (
          <div key={exam.id} style={{ ...S.examCard, boxShadow: "0 2px 8px rgba(0,0,0,0.06)" }}
            onClick={() => setSelected(exam)}>
            {exam.featured && <div style={{ position: "absolute", top: 12, right: 12, ...S.badge(colors.accent) }}>⭐ Featured</div>}
            <div style={{ fontSize: 40, marginBottom: 10 }}>{exam.icon}</div>
            <div style={{ fontSize: 17, fontWeight: 700, color: colors.text, marginBottom: 4 }}>{exam.name}</div>
            <div style={{ fontSize: 13, color: colors.muted, marginBottom: 12, lineHeight: 1.5 }}>{exam.description}</div>
            <div style={{ display: "flex", gap: 6, flexWrap: "wrap", marginBottom: 12 }}>
              <span style={S.badge(colors.primary)}>{exam.category}</span>
              {exam.examDate && <span style={S.badge(colors.accent)}>{exam.examDate}</span>}
            </div>
            {role === "admin" && (
              <div style={{ display: "flex", gap: 8, marginTop: 12, borderTop: `1px solid ${colors.border}`, paddingTop: 12 }} onClick={e => e.stopPropagation()}>
                <button onClick={() => openEdit(exam)} style={S.btn("secondary")}><Icon name="edit" size={13} /> Edit</button>
                <button onClick={() => deleteExam(exam.id)} style={S.btn("danger")}><Icon name="delete" size={13} /></button>
              </div>
            )}
          </div>
        ))}
      </div>

      {modal && (
        <Modal title={modal === "add" ? "Add New Exam" : "Edit Exam"} onClose={() => setModal(null)}>
          <FormField label="Exam Name *" field="name" placeholder="e.g. UPSC CSE" />
          <FormField label="Category *" field="category" placeholder="e.g. Government, Engineering, Medical" />
          <FormField label="Icon (Emoji)" field="icon" placeholder="📋" />
          <FormField label="Description" field="description" type="textarea" placeholder="Brief description of the exam" />
          <FormField label="Syllabus Overview" field="syllabus" type="textarea" placeholder="Main subjects and topics covered" />
          <FormField label="Exam Date" field="examDate" placeholder="e.g. May 2025" />
          <FormField label="Total Seats" field="totalSeats" type="number" placeholder="e.g. 1056" />
          <FormField label="Featured" field="featured" type="checkbox" />
          <div style={{ display: "flex", gap: 10, marginTop: 8 }}>
            <button onClick={saveExam} style={S.btn("primary")}>💾 Save Exam</button>
            <button onClick={() => setModal(null)} style={S.btn("secondary")}>Cancel</button>
          </div>
        </Modal>
      )}
    </div>
  );
}

// ─── PYQs PAGE ────────────────────────────────────────────────────────────────
function PYQsPage({ questions, setQuestions, exams, role }) {
  const [modal, setModal] = useState(null);
  const [form, setForm] = useState({});
  const [filter, setFilter] = useState({ examId: "", subject: "", year: "", difficulty: "" });
  const [expanded, setExpanded] = useState(null);

  const filtered = questions.filter(q =>
    (!filter.examId || q.examId === parseInt(filter.examId)) &&
    (!filter.subject || q.subject.toLowerCase().includes(filter.subject.toLowerCase())) &&
    (!filter.year || q.year === parseInt(filter.year)) &&
    (!filter.difficulty || q.difficulty === filter.difficulty)
  );

  const openAdd = () => {
    setForm({ id: Date.now(), examId: "", year: new Date().getFullYear(), subject: "", question: "", options: ["", "", "", ""], answer: 0, explanation: "", difficulty: "Medium", tags: "" });
    setModal("add");
  };
  const openEdit = (q) => { setForm({ ...q, tags: q.tags.join(", "), options: [...q.options] }); setModal("edit"); };
  const saveQ = () => {
    const q = { ...form, examId: parseInt(form.examId), year: parseInt(form.year), answer: parseInt(form.answer), tags: form.tags.split(",").map(t => t.trim()).filter(Boolean) };
    if (modal === "add") setQuestions(p => [...p, q]);
    else setQuestions(p => p.map(x => x.id === q.id ? q : x));
    setModal(null);
  };
  const deleteQ = (id) => { if (confirm("Delete this question?")) setQuestions(p => p.filter(q => q.id !== id)); };

  const diffColor = { Easy: colors.success, Medium: colors.accent, Hard: colors.danger };

  return (
    <div>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 20 }}>
        <div>
          <div style={S.heading}>PYQ Bank</div>
          <div style={S.subheading}>{filtered.length} questions • Previous Year Questions</div>
        </div>
        {role === "admin" && <button onClick={openAdd} style={S.btn("primary")}><Icon name="add" /> Add Question</button>}
      </div>

      <div style={{ ...S.card, display: "flex", gap: 12, flexWrap: "wrap" }}>
        <select value={filter.examId} onChange={e => setFilter(p => ({ ...p, examId: e.target.value }))} style={{ ...S.input, width: 180 }}>
          <option value="">All Exams</option>
          {exams.map(e => <option key={e.id} value={e.id}>{e.name}</option>)}
        </select>
        <input placeholder="Subject..." value={filter.subject} onChange={e => setFilter(p => ({ ...p, subject: e.target.value }))} style={{ ...S.input, width: 140 }} />
        <input placeholder="Year..." type="number" value={filter.year} onChange={e => setFilter(p => ({ ...p, year: e.target.value }))} style={{ ...S.input, width: 100 }} />
        <select value={filter.difficulty} onChange={e => setFilter(p => ({ ...p, difficulty: e.target.value }))} style={{ ...S.input, width: 140 }}>
          <option value="">All Difficulty</option>
          {["Easy", "Medium", "Hard"].map(d => <option key={d}>{d}</option>)}
        </select>
        <button onClick={() => setFilter({ examId: "", subject: "", year: "", difficulty: "" })} style={S.btn("outline")}>Clear</button>
      </div>

      {filtered.map((q, i) => (
        <div key={q.id} style={{ ...S.card, marginBottom: 12 }}>
          <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", gap: 12 }}>
            <div style={{ flex: 1 }}>
              <div style={{ display: "flex", gap: 8, flexWrap: "wrap", marginBottom: 8 }}>
                <span style={S.badge(colors.primary)}>{exams.find(e => e.id === q.examId)?.name || "Unknown"}</span>
                <span style={S.badge(colors.muted)}>{q.subject}</span>
                <span style={S.badge(colors.text)}>{q.year}</span>
                <span style={S.badge(diffColor[q.difficulty] || colors.accent)}>{q.difficulty}</span>
              </div>
              <div style={{ fontWeight: 600, fontSize: 14, color: colors.text, lineHeight: 1.6 }}>Q{i + 1}. {q.question}</div>
              {expanded === q.id && (
                <div style={{ marginTop: 14 }}>
                  {q.options.map((opt, idx) => (
                    <div key={idx} style={{ padding: "8px 12px", borderRadius: 8, marginBottom: 6, fontSize: 14, background: idx === q.answer ? colors.success + "20" : colors.bg, border: `1px solid ${idx === q.answer ? colors.success : colors.border}`, color: idx === q.answer ? colors.success : colors.text, fontWeight: idx === q.answer ? 700 : 400 }}>
                      {String.fromCharCode(65 + idx)}. {opt} {idx === q.answer && "✓"}
                    </div>
                  ))}
                  {q.explanation && (
                    <div style={{ marginTop: 12, padding: "12px", background: "#FFF7ED", borderRadius: 8, borderLeft: `4px solid ${colors.accent}` }}>
                      <div style={{ fontSize: 12, fontWeight: 700, color: colors.accent, marginBottom: 4 }}>EXPLANATION</div>
                      <div style={{ fontSize: 13, color: colors.text }}>{q.explanation}</div>
                    </div>
                  )}
                  <div style={{ marginTop: 10, display: "flex", gap: 6, flexWrap: "wrap" }}>
                    {q.tags.map(tag => <span key={tag} style={S.tag}>{tag}</span>)}
                  </div>
                </div>
              )}
            </div>
            <div style={{ display: "flex", gap: 6, flexShrink: 0 }}>
              <button onClick={() => setExpanded(expanded === q.id ? null : q.id)} style={S.btn("secondary")}>
                {expanded === q.id ? "Hide" : "View"}
              </button>
              {role === "admin" && <>
                <button onClick={() => openEdit(q)} style={S.btn("secondary")}><Icon name="edit" size={13} /></button>
                <button onClick={() => deleteQ(q.id)} style={S.btn("danger")}><Icon name="delete" size={13} /></button>
              </>}
            </div>
          </div>
        </div>
      ))}

      {modal && (
        <Modal title={modal === "add" ? "Add PYQ" : "Edit Question"} onClose={() => setModal(null)}>
          <div style={{ marginBottom: 14 }}>
            <label style={S.label}>Exam *</label>
            <select value={form.examId || ""} onChange={e => setForm(p => ({ ...p, examId: e.target.value }))} style={S.input}>
              <option value="">Select Exam</option>
              {exams.map(e => <option key={e.id} value={e.id}>{e.name}</option>)}
            </select>
          </div>
          {[["Subject *", "subject", "text", "e.g. Physics"], ["Year *", "year", "number", "2023"], ["Question *", "question", "textarea", "Enter the full question"]].map(([label, field, type, ph]) => (
            <div key={field} style={{ marginBottom: 14 }}>
              <label style={S.label}>{label}</label>
              {type === "textarea"
                ? <textarea value={form[field] || ""} onChange={e => setForm(p => ({ ...p, [field]: e.target.value }))} placeholder={ph} rows={3} style={{ ...S.input, resize: "vertical" }} />
                : <input type={type} value={form[field] || ""} onChange={e => setForm(p => ({ ...p, [field]: e.target.value }))} placeholder={ph} style={S.input} />
              }
            </div>
          ))}
          <div style={{ marginBottom: 14 }}>
            <label style={S.label}>Options (A, B, C, D)</label>
            {(form.options || ["", "", "", ""]).map((opt, i) => (
              <div key={i} style={{ display: "flex", gap: 8, marginBottom: 8, alignItems: "center" }}>
                <span style={{ width: 24, fontWeight: 700, color: colors.primary }}>{String.fromCharCode(65 + i)}.</span>
                <input value={opt} onChange={e => setForm(p => { const o = [...p.options]; o[i] = e.target.value; return { ...p, options: o }; })} placeholder={`Option ${String.fromCharCode(65 + i)}`} style={S.input} />
              </div>
            ))}
          </div>
          <div style={{ marginBottom: 14 }}>
            <label style={S.label}>Correct Answer</label>
            <select value={form.answer ?? 0} onChange={e => setForm(p => ({ ...p, answer: parseInt(e.target.value) }))} style={S.input}>
              {[0, 1, 2, 3].map(i => <option key={i} value={i}>Option {String.fromCharCode(65 + i)}</option>)}
            </select>
          </div>
          <div style={{ marginBottom: 14 }}>
            <label style={S.label}>Difficulty</label>
            <select value={form.difficulty || "Medium"} onChange={e => setForm(p => ({ ...p, difficulty: e.target.value }))} style={S.input}>
              {["Easy", "Medium", "Hard"].map(d => <option key={d}>{d}</option>)}
            </select>
          </div>
          <div style={{ marginBottom: 14 }}>
            <label style={S.label}>Explanation</label>
            <textarea value={form.explanation || ""} onChange={e => setForm(p => ({ ...p, explanation: e.target.value }))} rows={3} placeholder="Detailed explanation for the answer" style={{ ...S.input, resize: "vertical" }} />
          </div>
          <div style={{ marginBottom: 14 }}>
            <label style={S.label}>Tags (comma separated)</label>
            <input value={form.tags || ""} onChange={e => setForm(p => ({ ...p, tags: e.target.value }))} placeholder="e.g. History, Modern India, 1857" style={S.input} />
          </div>
          <div style={{ display: "flex", gap: 10 }}>
            <button onClick={saveQ} style={S.btn("primary")}>💾 Save Question</button>
            <button onClick={() => setModal(null)} style={S.btn("secondary")}>Cancel</button>
          </div>
        </Modal>
      )}
    </div>
  );
}

// ─── COURSES PAGE ─────────────────────────────────────────────────────────────
function CoursesPage({ courses, setCourses, exams, role }) {
  const [modal, setModal] = useState(null);
  const [form, setForm] = useState({});
  const [filterExam, setFilterExam] = useState("");

  const filtered = courses.filter(c => !filterExam || c.examId === parseInt(filterExam));

  const openAdd = () => {
    setForm({ id: Date.now(), examId: "", title: "", instructor: "", duration: "", price: 0, originalPrice: 0, enrolled: 0, rating: 4.5, description: "", topics: "", isFree: false });
    setModal(true);
  };
  const openEdit = (c) => { setForm({ ...c, topics: c.topics.join(", ") }); setModal(true); };
  const saveCourse = () => {
    const c = { ...form, examId: parseInt(form.examId), price: parseFloat(form.price), originalPrice: parseFloat(form.originalPrice), enrolled: parseInt(form.enrolled), rating: parseFloat(form.rating), topics: form.topics.split(",").map(t => t.trim()).filter(Boolean) };
    if (!c.id || modal === "add") setForm(p => ({ ...p, id: Date.now() }));
    if (courses.find(x => x.id === c.id)) setCourses(p => p.map(x => x.id === c.id ? c : x));
    else setCourses(p => [...p, c]);
    setModal(null);
  };
  const deleteCourse = (id) => { if (confirm("Delete this course?")) setCourses(p => p.filter(c => c.id !== id)); };

  return (
    <div>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 20 }}>
        <div>
          <div style={S.heading}>Courses</div>
          <div style={S.subheading}>{filtered.length} courses available</div>
        </div>
        {role === "admin" && <button onClick={openAdd} style={S.btn("primary")}><Icon name="add" /> Add Course</button>}
      </div>

      <div style={{ marginBottom: 16 }}>
        <select value={filterExam} onChange={e => setFilterExam(e.target.value)} style={{ ...S.input, width: 220 }}>
          <option value="">All Exams</option>
          {exams.map(e => <option key={e.id} value={e.id}>{e.name}</option>)}
        </select>
      </div>

      <div style={S.grid(2)}>
        {filtered.map(c => (
          <div key={c.id} style={S.card}>
            <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 8 }}>
              <span style={S.badge(colors.primary)}>{exams.find(e => e.id === c.examId)?.name}</span>
              {c.isFree ? <span style={S.badge(colors.success)}>FREE</span> : <span style={S.badge(colors.accent)}>PAID</span>}
            </div>
            <div style={{ fontSize: 16, fontWeight: 700, color: colors.text, marginBottom: 4 }}>{c.title}</div>
            <div style={{ fontSize: 13, color: colors.muted, marginBottom: 10 }}>👨‍🏫 {c.instructor} • ⏱️ {c.duration}</div>
            <div style={{ fontSize: 13, color: colors.muted, marginBottom: 12, lineHeight: 1.6 }}>{c.description}</div>
            <div style={{ display: "flex", gap: 4, flexWrap: "wrap", marginBottom: 14 }}>
              {c.topics.map(t => <span key={t} style={S.tag}>{t}</span>)}
            </div>
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center" }}>
              <div>
                <span style={{ fontSize: 20, fontWeight: 800, color: c.isFree ? colors.success : colors.primary }}>
                  {c.isFree ? "Free" : `₹${c.price.toLocaleString()}`}
                </span>
                {!c.isFree && c.originalPrice > c.price && (
                  <span style={{ fontSize: 13, color: colors.muted, textDecoration: "line-through", marginLeft: 8 }}>₹{c.originalPrice.toLocaleString()}</span>
                )}
              </div>
              <div style={{ fontSize: 13, color: colors.muted }}>⭐ {c.rating} • {c.enrolled.toLocaleString()} enrolled</div>
            </div>
            {role === "admin" && (
              <div style={{ display: "flex", gap: 8, marginTop: 14, paddingTop: 12, borderTop: `1px solid ${colors.border}` }}>
                <button onClick={() => openEdit(c)} style={S.btn("secondary")}><Icon name="edit" size={13} /> Edit</button>
                <button onClick={() => deleteCourse(c.id)} style={S.btn("danger")}><Icon name="delete" size={13} /></button>
              </div>
            )}
            {role === "student" && (
              <button style={{ ...S.btn("primary"), width: "100%", justifyContent: "center", marginTop: 14 }}>
                {c.isFree ? "Start Free" : "Enroll Now"} →
              </button>
            )}
          </div>
        ))}
      </div>

      {modal && (
        <Modal title="Course Details" onClose={() => setModal(null)}>
          {[["Course Title *", "title", "text", "e.g. UPSC Foundation Batch 2025"],
            ["Instructor *", "instructor", "text", "e.g. Dr. Rajesh Kumar"],
            ["Duration", "duration", "text", "e.g. 12 months"],
            ["Description", "description", "textarea", "What will students learn?"],
            ["Topics (comma separated)", "topics", "text", "e.g. History, Geography, Economy"]].map(([label, field, type, ph]) => (
            <div key={field} style={{ marginBottom: 14 }}>
              <label style={S.label}>{label}</label>
              {type === "textarea"
                ? <textarea value={form[field] || ""} onChange={e => setForm(p => ({ ...p, [field]: e.target.value }))} placeholder={ph} rows={3} style={{ ...S.input, resize: "vertical" }} />
                : <input type="text" value={form[field] || ""} onChange={e => setForm(p => ({ ...p, [field]: e.target.value }))} placeholder={ph} style={S.input} />
              }
            </div>
          ))}
          <div style={{ marginBottom: 14 }}>
            <label style={S.label}>Exam</label>
            <select value={form.examId || ""} onChange={e => setForm(p => ({ ...p, examId: e.target.value }))} style={S.input}>
              <option value="">Select Exam</option>
              {exams.map(e => <option key={e.id} value={e.id}>{e.name}</option>)}
            </select>
          </div>
          <div style={{ display: "flex", gap: 12, marginBottom: 14 }}>
            <div style={{ flex: 1 }}>
              <label style={S.label}>Price (₹)</label>
              <input type="number" value={form.price || 0} onChange={e => setForm(p => ({ ...p, price: e.target.value }))} style={S.input} />
            </div>
            <div style={{ flex: 1 }}>
              <label style={S.label}>Original Price (₹)</label>
              <input type="number" value={form.originalPrice || 0} onChange={e => setForm(p => ({ ...p, originalPrice: e.target.value }))} style={S.input} />
            </div>
            <div style={{ flex: 1 }}>
              <label style={S.label}>Rating</label>
              <input type="number" min="0" max="5" step="0.1" value={form.rating || 4.5} onChange={e => setForm(p => ({ ...p, rating: e.target.value }))} style={S.input} />
            </div>
          </div>
          <div style={{ marginBottom: 14 }}>
            <label style={{ display: "flex", alignItems: "center", gap: 8, cursor: "pointer" }}>
              <input type="checkbox" checked={form.isFree || false} onChange={e => setForm(p => ({ ...p, isFree: e.target.checked, price: e.target.checked ? 0 : p.price })) } />
              <span style={{ fontWeight: 600, fontSize: 14 }}>Free Course</span>
            </label>
          </div>
          <div style={{ display: "flex", gap: 10 }}>
            <button onClick={saveCourse} style={S.btn("primary")}>💾 Save Course</button>
            <button onClick={() => setModal(null)} style={S.btn("secondary")}>Cancel</button>
          </div>
        </Modal>
      )}
    </div>
  );
}

// ─── ADMIN PANEL ──────────────────────────────────────────────────────────────
function AdminPanel({ exams, questions, courses }) {
  const categories = [...new Set(exams.map(e => e.category))];
  return (
    <div>
      <div style={S.heading}>Admin Overview</div>
      <div style={S.subheading}>Platform management dashboard</div>
      <div style={S.grid(4)}>
        {[{ label: "Total Exams", v: exams.length, c: colors.primary }, { label: "Total Questions", v: questions.length, c: colors.accent }, { label: "Total Courses", v: courses.length, c: colors.success }, { label: "Categories", v: categories.length, c: "#8B5CF6" }].map(s => (
          <div key={s.label} style={{ ...S.statCard, borderTop: `4px solid ${s.c}` }}>
            <div style={{ fontSize: 28, fontWeight: 800, color: s.c }}>{s.v}</div>
            <div style={{ fontSize: 13, color: colors.muted, marginTop: 4 }}>{s.label}</div>
          </div>
        ))}
      </div>
      <div style={S.card}>
        <div style={{ fontWeight: 700, marginBottom: 14, fontSize: 16 }}>📋 Exams by Category</div>
        {categories.map(cat => (
          <div key={cat} style={{ display: "flex", justifyContent: "space-between", padding: "10px 0", borderBottom: `1px solid ${colors.border}`, fontSize: 14 }}>
            <span>{cat}</span>
            <span style={S.badge(colors.primary)}>{exams.filter(e => e.category === cat).length} exams</span>
          </div>
        ))}
      </div>
      <div style={S.card}>
        <div style={{ fontWeight: 700, marginBottom: 14, fontSize: 16 }}>📊 Quick Stats</div>
        <div style={{ fontSize: 14, color: colors.muted, lineHeight: 2 }}>
          • Free courses: {courses.filter(c => c.isFree).length}<br />
          • Paid courses: {courses.filter(c => !c.isFree).length}<br />
          • Featured exams: {exams.filter(e => e.featured).length}<br />
          • Total PYQ years: {[...new Set(questions.map(q => q.year))].length}<br />
          • Avg rating: {courses.length ? (courses.reduce((a, c) => a + c.rating, 0) / courses.length).toFixed(1) : "N/A"}
        </div>
      </div>
    </div>
  );
}

// ─── MAIN APP ─────────────────────────────────────────────────────────────────
export default function App() {
  const [user, setUser] = useState(null);
  const [page, setPage] = useState("home");
  const [exams, setExams] = useState(initialExams);
  const [questions, setQuestions] = useState(initialQuestions);
  const [courses, setCourses] = useState(initialCourses);

  if (!user) return <LoginPage onLogin={role => { setUser(role); setPage("home"); }} />;

  const studentNav = [
    { id: "home", label: "Dashboard", icon: "🏠" },
    { id: "exams", label: "Exams", icon: "📋" },
    { id: "pyqs", label: "PYQ Bank", icon: "❓" },
    { id: "courses", label: "Courses", icon: "📚" },
  ];

  const adminNav = [
    ...studentNav,
    { id: "admin", label: "Admin Panel", icon: "⚙️" },
  ];

  const nav = user === "admin" ? adminNav : studentNav;

  const renderPage = () => {
    switch (page) {
      case "home": return <Dashboard exams={exams} questions={questions} courses={courses} role={user} />;
      case "exams": return <ExamsPage exams={exams} setExams={setExams} role={user} questions={questions} courses={courses} />;
      case "pyqs": return <PYQsPage questions={questions} setQuestions={setQuestions} exams={exams} role={user} />;
      case "courses": return <CoursesPage courses={courses} setCourses={setCourses} exams={exams} role={user} />;
      case "admin": return user === "admin" ? <AdminPanel exams={exams} questions={questions} courses={courses} /> : null;
      default: return null;
    }
  };

  return (
    <div style={S.app}>
      <div style={S.sidebar}>
        <div style={S.sidebarBrand}>
          <div style={S.brandLogo}>📚 ExamHub</div>
          <div style={S.brandSub}>Exam Preparation Platform</div>
        </div>
        <div style={S.sidebarNav}>
          <div style={S.navSection}>Menu</div>
          {nav.map(item => (
            <div key={item.id} style={S.navItem(page === item.id)} onClick={() => setPage(item.id)}>
              <span>{item.icon}</span>
              <span>{item.label}</span>
            </div>
          ))}
          <div style={{ marginTop: 20 }}>
            <div style={S.navSection}>Account</div>
            <div style={S.navItem(false)} onClick={() => setUser(null)}>
              <span>🚪</span><span>Logout</span>
            </div>
          </div>
        </div>
        <div style={{ padding: "12px 16px", borderTop: "1px solid #2D2D4E" }}>
          <div style={{ fontSize: 12, color: "#94A3B8" }}>Logged in as</div>
          <div style={{ fontSize: 13, color: "#fff", fontWeight: 600, marginTop: 2 }}>{user === "admin" ? "👑 Admin" : "👤 Student"}</div>
        </div>
      </div>

      <div style={S.main}>
        <div style={S.topbar}>
          <div style={{ fontSize: 15, fontWeight: 700, color: colors.text }}>
            {nav.find(n => n.id === page)?.icon} {nav.find(n => n.id === page)?.label}
          </div>
          <div style={{ display: "flex", gap: 10, alignItems: "center" }}>
            {user === "admin" && <span style={S.badge(colors.accent)}>Admin Mode</span>}
            <div style={{ width: 34, height: 34, borderRadius: "50%", background: colors.primary, display: "flex", alignItems: "center", justifyContent: "center", color: "#fff", fontWeight: 700, fontSize: 14 }}>
              {user === "admin" ? "A" : "S"}
            </div>
          </div>
        </div>
        <div style={S.content}>{renderPage()}</div>
      </div>
    </div>
  );
}
