// 🔑 Auth Context
const AuthContext = createContext();
function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const unsub = onAuthStateChanged(auth, (currentUser) => {
      setUser(currentUser);
      setLoading(false);
    });
    return () => unsub();
  }, []);

  return <AuthContext.Provider value={{ user }}>{!loading && children}</AuthContext.Provider>;
}
function useAuth() {
  return useContext(AuthContext);
}

// 🔐 Auth Form
function AuthForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [isLogin, setIsLogin] = useState(true);

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      if (isLogin) {
        await signInWithEmailAndPassword(auth, email, password);
      } else {
        await createUserWithEmailAndPassword(auth, email, password);
      }
    } catch (err) {
      alert(err.message);
    }
  };

  return (
    <div className="flex flex-col gap-4 max-w-sm mx-auto mt-20 bg-white p-6 rounded-2xl shadow">
      <h2 className="text-xl font-bold">{isLogin ? "Login" : "Sign Up"}</h2>
      <form onSubmit={handleSubmit} className="flex flex-col gap-2">
        <input
          type="email"
          placeholder="Email"
          className="border p-2 rounded"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
        />
        <input
          type="password"
          placeholder="Password"
          className="border p-2 rounded"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
        />
        <button className="bg-blue-600 text-white py-2 rounded">
          {isLogin ? "Login" : "Sign Up"}
        </button>
      </form>
      <button onClick={() => setIsLogin(!isLogin)} className="text-sm text-blue-600 underline">
        {isLogin ? "Need an account? Sign Up" : "Have an account? Login"}
      </button>
    </div>
  );
}

// 🎮 Free Fire Panel (same as before, mock data)
function FreeFirePanel() {
  const [users, setUsers] = useState([]);
  const [announcements, setAnnouncements] = useState([]);
  const [stats, setStats] = useState({ activeUsers: 0, matchesToday: 0, redeemRequests: 0 });
  const [newAnnouncement, setNewAnnouncement] = useState("");
  const [redeemCode, setRedeemCode] = useState("");
  const [tournaments, setTournaments] = useState([]);

  useEffect(() => {
    setUsers([
      { id: 1, name: "AlphaPlayer", rank: "Gold IV", coins: 120 },
      { id: 2, name: "BetaKing", rank: "Platinum II", coins: 420 },
      { id: 3, name: "GammaX", rank: "Diamond V", coins: 980 }
    ]);
    setAnnouncements([{ id: 1, title: "Server Maintenance", body: "Server will be down at 2 AM IST." }]);
    setStats({ activeUsers: 234, matchesToday: 58, redeemRequests: 7 });
    setTournaments([{ id: 1, name: "Weekend Clash", date: "2025-10-04", slots: 16 }]);
  }, []);

  function addAnnouncement(e) {
    e.preventDefault();
    if (!newAnnouncement.trim()) return;
    const a = { id: Date.now(), title: newAnnouncement.slice(0, 30), body: newAnnouncement };
    setAnnouncements([a, ...announcements]);
    setNewAnnouncement("");
  }

  function submitRedeem(e) {
    e.preventDefault();
    if (!redeemCode.trim()) return alert("Enter code");
    const ok = redeemCode === "FREE50";
    alert(ok ? "Redeem successful (mock)" : "Invalid code (mock)");
    setRedeemCode("");
    setStats((s) => ({ ...s, redeemRequests: s.redeemRequests + 1 }));
  }

  function addTournament(e) {
    e.preventDefault();
    const form = e.target;
    const name = form.name.value;
    const date = form.date.value;
    const slots = Number(form.slots.value || 8);
    if (!name || !date) return alert("Fill name and date");
    setTournaments([{ id: Date.now(), name, date, slots }, ...tournaments]);
    form.reset();
  }

  return (
    <div className="min-h-screen bg-gray-100 text-gray-900 p-6">
      <div className="flex justify-end mb-4">
        <button onClick={() => signOut(auth)} className="px-3 py-1 bg-red-600 text-white rounded">
          Logout
        </button>
      </div>
      <div className="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-4 gap-6">
        {/* Sidebar */}
        <aside className="md:col-span-1 bg-white p-4 rounded-2xl shadow-sm">
          <h2 className="text-xl font-bold mb-4">FF Panel</h2>
          <nav className="space-y-2">
            <button className="w-full text-left px-3 py-2 rounded hover:bg-gray-50">Dashboard</button>
            <button className="w-full text-left px-3 py-2 rounded hover:bg-gray-50">Users</button>
            <button className="w-full text-left px-3 py-2 rounded hover:bg-gray-50">Announcements</button>
            <button className="w-full text-left px-3 py-2 rounded hover:bg-gray-50">Redeem</button>
            <button className="w-full text-left px-3 py-2 rounded hover:bg-gray-50">Tournaments</button>
          </nav>
        </aside>

        {/* Main */}
        <main className="md:col-span-3 space-y-6">
          {/* Stats */}
          <section className="grid grid-cols-1 sm:grid-cols-3 gap-4">
            <div className="bg-white p-4 rounded-2xl shadow-sm">
              <div className="text-sm text-gray-500">Active Users</div>
              <div className="text-2xl font-bold">{stats.activeUsers}</div>
            </div>
            <div className="bg-white p-4 rounded-2xl shadow-sm">
              <div className="text-sm text-gray-500">Matches Today</div>
              <div className="text-2xl font-bold">{stats.matchesToday}</div>
            </div>
            <div className="bg-white p-4 rounded-2xl shadow-sm">
              <div className="text-sm text-gray-500">Redeem Requests</div>
              <div className="text-2xl font-bold">{stats.redeemRequests}</div>
            </div>
          </section>

          {/* Announcements */}
          <section className="grid grid-cols-1 lg:grid-cols-2 gap-4">
            <div className="bg-white p-4 rounded-2xl shadow-sm">
              <h3 className="font-semibold mb-3">Announcements</h3>
              <form onSubmit={addAnnouncement} className="space-y-2">
                <input
                  value={newAnnouncement}
                  onChange={(e) => setNewAnnouncement(e.target.value)}
                  placeholder="Write an announcement..."
                  className="w-full border p-2 rounded"
                />
                <button className="px-3 py-1 bg-blue-600 text-white rounded">Post</button>
              </form>
              <div className="mt-4 space-y-3">
                {announcements.map((a) => (
                  <div key={a.id} className="border p-3 rounded">
                    <div className="font-semibold">{a.title}</div>
                    <div className="text-sm text-gray-600">{a.body}</div>
                  </div>
                ))}
              </div>
            </div>

            <div className="bg-white p-4 rounded-2xl shadow-sm">
              <h3 className="font-semibold mb-3">Redeem Code (Mock)</h3>
              <form onSubmit={submitRedeem} className="flex gap-2">
                <input
                  value={redeemCode}
                  onChange={(e) => setRedeemCode(e.target.value)}
                  placeholder="Enter code"
                  className="flex-1 border p-2 rounded"
                />
                <button className="px-3 py-1 bg-green-600 text-white rounded">Submit</button>
              </form>
              <p className="text-xs text-gray-500 mt-2">
                Try code <strong>FREE50</strong> for mock success.
              </p>
            </div>
          </section>

          {/* Users */}
          <section className="bg-white p-4 rounded-2xl shadow-sm">
            <h3 className="font-semibold mb-3">Users</h3>
            <div className="grid gap-3">
              {users.map((u) => (
                <div key={u.id} className="flex items-center justify-between border p-3 rounded">
                  <div>
                    <div className="font-medium">{u.name}</div>
                    <div className="text-sm text-gray-500">
                      {u.rank} • {u.coins} coins
                    </div>
                  </div>
                  <div className="flex gap-2">
                    <button className="px-2 py-1 border rounded text-sm">Edit</button>
                    <button className="px-2 py-1 border rounded text-sm">Ban</button>
                  </div>
                </div>
              ))}
            </div>
          </section>

          {/* Tournaments */}
          <section className="bg-white p-4 rounded-2xl shadow-sm">
            <div className="flex items-center justify-between">
              <h3 className="font-semibold">Tournaments</h3>
              <form onSubmit={addTournament} className="flex gap-2">
                <input name="name" placeholder="Name" className="border p-1 rounded" />
                <input name="date" type="date" className="border p-1 rounded" />
                <input name="slots" placeholder="Slots" type="number" className="w-20 border p-1 rounded" />
                <button className="px-3 py-1 bg-indigo-600 text-white rounded">Add</button>
              </form>
            </div>
            <div className="mt-4 space-y-2">
              {tournaments.map((t) => (
                <div key={t.id} className="border p-3 rounded flex justify-between">
                  <div>
                    <div className="font-medium">{t.name}</div>
                    <div className="text-sm text-gray-500">{t.date} • {t.slots} slots</div>
                  </div>
                  <div className="text-sm">Actions</div>
                </div>
              ))}
            </div>
          </section>
        </main>
      </div>
    </div>
  );
}

// 🚀 Root App
export default function App() {
  const { user } = useAuth();
  return (
    <AuthProvider>{user ? <FreeFirePanel /> : <AuthForm />}</AuthProvider>
  );
}
