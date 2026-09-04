import React, { useState, useEffect } from 'react';
import { 
    Compass, Users, School, Target, ClipboardList, CalendarDays, 
    LogOut, Edit2, Check, X, Plus, Calendar as CalendarIcon, UserPen
} from 'lucide-react';
import { initializeApp } from 'firebase/app';
import { getAuth, signInWithCustomToken, signInAnonymously, onAuthStateChanged, signOut } from 'firebase/auth';
import { getFirestore, collection, onSnapshot, doc, updateDoc, addDoc, deleteDoc } from 'firebase/firestore';

// ==========================================
// 1. Firebase 初始化 (嚴格遵守環境規範)
// ==========================================
const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const appId = typeof __app_id !== 'undefined' ? __app_id : 'career-planning-app';

// 預設資料 (若資料庫為空時寫入)
const defaultTasks = [
    { taskId: "2.1.1", name: "善用校本及外間資源", category: "學校關注事項", pic: ["Wong ML", "Cheng KH", "Kung PL", "Chan KW"], month: 9 },
    { taskId: "2.2.1", name: "整合校內外實務與工作場景學習", category: "學校關注事項", pic: ["Mak CS", "Chau PY", "Cheng KH"], month: 10 },
    { taskId: "2.2.2", name: "透過活動策劃訓練與反思練習", category: "學校關注事項", pic: ["Cheng KH", "Chan KY", "Leung TK"], month: 11 },
    { taskId: "2.3.1", name: "優化「我的成長印記」和「成績表360」", category: "學校關注事項", pic: ["Wong ML"], month: 1 },
    { taskId: "2.3.2", name: "推行「能力積木」微證書課程計劃", category: "學校關注事項", pic: ["Wong ML"], month: 2 },
    { taskId: "2.3.4", name: "透過多元活動，讓家長了解多元出路", category: "學校關注事項", pic: ["Chan LH"], month: 3 },
    { taskId: "3.2.1", name: "安老業系列活動", category: "學校關注事項", pic: ["Mak CS", "Tsang LK"], month: 11 },
    { taskId: "A1", name: "中五六級院校參觀", category: "組本關注事項", pic: ["Chan KY", "Chu PY"], month: 10 },
    { taskId: "B1", name: "為中四五六級學生提供小組支援與輔導", category: "組本關注事項", pic: ["Wong ML", "Chu PY", "Wong CN", "Chan WC"], month: 9 },
    { taskId: "B2", name: "中六生升學支援個別及小組面見", category: "組本關注事項", pic: ["Chan LH", "Wong ML", "Hon WK", "Tsang LK", "Cheng KH", "Ho TS", "班主任"], month: 11 },
    { taskId: "B3", name: "中三生選科個別面見", category: "組本關注事項", pic: ["班主任"], month: 3 },
    { taskId: "B4", name: "放榜前準備講座及放榜日支援", category: "組本關注事項", pic: ["Chan LH", "所有成員", "班主任"], month: 7 },
    { taskId: "C1", name: "中五課後職業影子工作坊", category: "組本關注事項", pic: ["Leung KY"], month: 4 },
    { taskId: "C2", name: "提供面試訓練計劃", category: "組本關注事項", pic: ["Chu PY"], month: 5 },
    { taskId: "C3", name: "堅趣計劃", category: "組本關注事項", pic: ["Ho TS"], month: 10 },
    { taskId: "C4", name: "開設職業探索活動與職業探索班", category: "組本關注事項", pic: ["Chau PY", "Ho TS"], month: 2 },
    { taskId: "C5", name: "半島南扶輪社師友計劃", category: "組本關注事項", pic: ["Chu PY", "Hon WK", "Wong CN"], month: 11 },
    { taskId: "C6", name: "參觀企業/院校計劃", category: "組本關注事項", pic: ["Ho TS", "所有成員"], month: 4 },
    { taskId: "C7", name: "生涯規劃工作坊", category: "組本關注事項", pic: ["Lui WM"], month: 3 },
    { taskId: "C8", name: "聯校咖啡拉花比賽及小學生朱古力拉花體驗", category: "組本關注事項", pic: ["Cheng KH", "Chan KY", "Leung TK"], month: 12 },
    { taskId: "C10", name: "主題式生涯規劃活動", category: "組本關注事項", pic: ["Hon WK", "Chan KY", "Wong CN"], month: 5 },
    { taskId: "C11", name: "試後職場參觀", category: "組本關注事項", pic: ["Lui WM"], month: 6 },
    { taskId: "D1", name: "校友及家長分享職涯/生涯故事", category: "組本關注事項", pic: ["Tsang LK", "Chan LH"], month: 3 },
    { taskId: "E1", name: "內地升學親子考察團", category: "組本關注事項", pic: ["Chan LH"], month: 4 },
    { taskId: "ZA", name: "學生推薦書及升學文件整理、獎助學金", category: "恆常工作項目", pic: ["Wong ML", "Chan LH", "Chu PY", "Hon WK", "Lui WM"], month: 9 },
    { taskId: "ZB", name: "SLP、呈交學校評核報告及校長推薦計劃評分", category: "恆常工作項目", pic: ["Wong ML", "班主任"], month: 10 },
    { taskId: "ZC", name: "各課程平台後台管理", category: "恆常工作項目", pic: ["Wong ML"], month: 9 },
    { taskId: "ZD", name: "升學及就業路向與輔導簡表整理", category: "恆常工作項目", pic: ["Chan LH", "Chan WC", "Wong ML", "班主任"], month: 11 },
    { taskId: "ZE", name: "職場學習紀錄存檔整理", category: "恆常工作項目", pic: ["Chau PY"], month: 6 },
    { taskId: "ZF", name: "生涯規劃課級聯絡", category: "恆常工作項目", pic: ["Chau PY", "Wong CN", "Chu PY", "Wong ML"], month: 9 },
    { taskId: "ZG", name: "班主任聯席會議", category: "恆常工作項目", pic: ["Wong ML", "Chan KY", "Tsang LK", "Chu PY", "Chan LH", "Cheng KH", "Chau PY"], month: 9 },
    { taskId: "ZH", name: "啟導會議及專業發展", category: "恆常工作項目", pic: ["Wong ML"], month: 9 },
    { taskId: "ZI", name: "校內檢討問卷及職業性向統計", category: "恆常工作項目", pic: ["Ho TS"], month: 5 },
    { taskId: "ZJ", name: "資源存檔、網頁和資源角更新", category: "恆常工作項目", pic: ["Hon WK"], month: 7 }
];

export default function App() {
    const [user, setUser] = useState(null);
    const [activeTab, setActiveTab] = useState('dashboard');
    const [tasks, setTasks] = useState([]);
    const [loading, setLoading] = useState(true);

    // ==========================================
    // 2. 身分驗證 (Auth)
    // ==========================================
    useEffect(() => {
        const initAuth = async () => {
            try {
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    await signInWithCustomToken(auth, __initial_auth_token);
                } else {
                    await signInAnonymously(auth);
                }
            } catch (err) {
                console.error("Auth Error:", err);
            }
        };
        initAuth();
        const unsubscribe = onAuthStateChanged(auth, (currentUser) => {
            setUser(currentUser);
        });
        return () => unsubscribe();
    }, []);

    // ==========================================
    // 3. 實時資料庫監聽 (Firestore Real-time)
    // ==========================================
    useEffect(() => {
        if (!user) return;
        setLoading(true);
        // 使用公共路徑，讓所有組員都能看見實時更新的任務
        const tasksRef = collection(db, 'artifacts', appId, 'public', 'data', 'tasks');
        
        const unsubscribe = onSnapshot(tasksRef, async (snapshot) => {
            let fetchedTasks = snapshot.docs.map(doc => ({ dbId: doc.id, ...doc.data() }));
            
            // 如果是空資料庫，自動寫入初始預設資料
            if (fetchedTasks.length === 0) {
                console.log("資料庫為空，正在匯入預設任務...");
                for (const t of defaultTasks) {
                    await addDoc(tasksRef, t);
                }
            } else {
                setTasks(fetchedTasks);
                setLoading(false);
            }
        }, (error) => {
            console.error("Firestore error:", error);
            setLoading(false);
        });

        return () => unsubscribe();
    }, [user]);

    const handleLogout = () => signOut(auth);

    // 尚未驗證或正在讀取
    if (!user) return <div className="min-h-screen flex items-center justify-center bg-gray-100">正在連接安全驗證...</div>;

    return (
        <div className="min-h-screen bg-gray-100 text-gray-800 font-sans antialiased">
            {/* 頁首 */}
            <header className="bg-gradient-to-r from-blue-900 to-blue-700 text-white py-10 px-4 shadow-lg relative overflow-hidden">
                <div className="absolute top-0 left-0 w-full h-full opacity-10 pattern-dots" style={{ backgroundImage: 'radial-gradient(#fff 1px, transparent 1px)', backgroundSize: '20px 20px' }}></div>
                <div className="max-w-7xl mx-auto relative z-10 flex flex-col md:flex-row justify-between items-center">
                    <div className="text-center md:text-left mb-4 md:mb-0">
                        <h1 className="text-3xl md:text-5xl font-bold mb-2 tracking-wider flex items-center justify-center md:justify-start">
                            <Compass className="mr-3 w-8 h-8 md:w-12 md:h-12" /> 生涯規劃教育組
                        </h1>
                        <p className="text-lg md:text-xl text-blue-100 font-light flex items-center justify-center md:justify-start">
                            <span className="bg-green-500 w-2 h-2 rounded-full mr-2 animate-pulse"></span>
                            實時協作平台 (已連線)
                        </p>
                    </div>
                    <div className="flex items-center bg-white/10 p-2 md:p-3 rounded-lg backdrop-blur-sm">
                        <div className="w-10 h-10 bg-blue-500 rounded-full flex items-center justify-center text-xl font-bold mr-3 shadow-inner">
                            {user.isAnonymous ? "訪" : "G"}
                        </div>
                        <div className="text-sm mr-4">
                            <p className="font-semibold">{user.isAnonymous ? "協作組員 (訪客模式)" : "Google 登入組員"}</p>
                            <p className="text-blue-200 text-xs truncate w-32">{user.uid}</p>
                        </div>
                    </div>
                </div>
            </header>

            {/* 導覽列 */}
            <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 -mt-6 relative z-20">
                <div className="bg-white rounded-xl shadow-md p-2 mb-8 flex flex-wrap justify-center gap-2">
                    <TabButton active={activeTab === 'dashboard'} onClick={() => setActiveTab('dashboard')} icon={<CalendarDays className="w-5 h-5 mr-2" />} text="任務儀表板" isPrimary />
                    <TabButton active={activeTab === 'members'} onClick={() => setActiveTab('members')} icon={<Users className="w-5 h-5 mr-2" />} text="核心小組成員" />
                    <TabButton active={activeTab === 'school'} onClick={() => setActiveTab('school')} icon={<School className="w-5 h-5 mr-2" />} text="學校關注事項" />
                    <TabButton active={activeTab === 'committee'} onClick={() => setActiveTab('committee')} icon={<Target className="w-5 h-5 mr-2" />} text="組本關注事項" />
                    <TabButton active={activeTab === 'routine'} onClick={() => setActiveTab('routine')} icon={<ClipboardList className="w-5 h-5 mr-2" />} text="恆常工作項目" />
                </div>

                {/* 內容區域 */}
                <div className="transition-all duration-300">
                    {activeTab === 'dashboard' && <DashboardTab tasks={tasks} loading={loading} db={db} appId={appId} />}
                    {activeTab === 'members' && <MembersTab />}
                    {activeTab === 'school' && <SchoolTab />}
                    {activeTab === 'committee' && <CommitteeTab />}
                    {activeTab === 'routine' && <RoutineTab />}
                </div>
            </main>
        </div>
    );
}

// ==========================================
// 共用組件
// ==========================================
function TabButton({ active, onClick, icon, text, isPrimary }) {
    const activeClass = isPrimary 
        ? "bg-teal-600 text-white border-teal-700 shadow-inner" 
        : "bg-blue-600 text-white shadow-inner";
    
    const inactiveClass = isPrimary
        ? "bg-teal-50 text-teal-700 hover:bg-teal-100 border border-teal-200"
        : "bg-gray-100 text-gray-600 hover:bg-gray-200";

    return (
        <button 
            onClick={onClick} 
            className={`flex-1 min-w-[150px] py-3 px-4 rounded-lg font-medium transition-all duration-200 flex items-center justify-center ${active ? activeClass : inactiveClass}`}
        >
            {icon} {text}
        </button>
    );
}

// ==========================================
// 分頁: 任務儀表板 (實時編輯與年曆)
// ==========================================
function DashboardTab({ tasks, loading, db, appId }) {
    const [filterCategory, setFilterCategory] = useState('all');
    const [filterPerson, setFilterPerson] = useState('all');
    const [viewMode, setViewMode] = useState('calendar');
    const [editingTask, setEditingTask] = useState(null);

    // 篩選邏輯
    const filteredTasks = tasks.filter(task => {
        const matchCategory = filterCategory === 'all' || task.category === filterCategory;
        const matchPerson = filterPerson === 'all' || 
            (task.pic && task.pic.some(p => 
                p.includes(filterPerson) || 
                (p === '所有成員' && !['Kung PL', 'Chan KW', 'Chan WC', 'Leung KY', '班主任'].includes(filterPerson))
            ));
        return matchCategory && matchPerson;
    });

    // 處理編輯儲存
    const handleSave = async (updatedTask) => {
        if (!updatedTask.dbId) return;
        const taskRef = doc(db, 'artifacts', appId, 'public', 'data', 'tasks', updatedTask.dbId);
        try {
            await updateDoc(taskRef, {
                name: updatedTask.name,
                taskId: updatedTask.taskId,
                month: parseInt(updatedTask.month),
                pic: updatedTask.pic,
                category: updatedTask.category
            });
            setEditingTask(null);
        } catch (e) {
            console.error("更新失敗:", e);
        }
    };

    return (
        <div className="bg-white rounded-xl shadow-md overflow-hidden border-t-4 border-teal-500 animation-fade-in">
            <div className="bg-teal-50 px-4 md:px-6 py-5 border-b border-teal-100 flex flex-col xl:flex-row xl:items-center justify-between gap-4">
                <h2 className="text-xl md:text-2xl font-bold text-teal-800 flex items-center">
                    <CalendarDays className="mr-3 w-6 h-6 md:w-8 md:h-8 text-teal-600" /> 
                    任務總覽與實時分配
                </h2>
                
                {/* 篩選器 */}
                <div className="flex flex-wrap gap-2 md:gap-3">
                    <select value={filterCategory} onChange={(e) => setFilterCategory(e.target.value)} className="bg-white border border-gray-300 text-gray-700 text-sm rounded-lg focus:ring-teal-500 p-2 md:p-2.5 shadow-sm">
                        <option value="all">📌 所有分類</option>
                        <option value="學校關注事項">學校關注事項</option>
                        <option value="組本關注事項">組本關注事項</option>
                        <option value="恆常工作項目">恆常工作項目</option>
                    </select>

                    <select value={filterPerson} onChange={(e) => setFilterPerson(e.target.value)} className="bg-white border border-gray-300 text-gray-700 text-sm rounded-lg focus:ring-teal-500 p-2 md:p-2.5 shadow-sm">
                        <option value="all">👤 所有人 (負責/協助)</option>
                        <optgroup label="核心成員">
                            <option value="Wong ML">黃文禮 (Wong ML)</option>
                            <option value="Cheng KH">鄭鍵鴻 (Cheng KH)</option>
                            <option value="Chan KY">陳家瑩 (Chan KY)</option>
                            <option value="Chan LH">陳麗興 (Chan LH)</option>
                            <option value="Chau PY">周貝兒 (Chau PY)</option>
                            <option value="Chu PY">朱寶兒 (Chu PY)</option>
                            <option value="Ho TS">何狄燊 (Ho TS)</option>
                            <option value="Hon WK">韓惠錦 (Hon WK)</option>
                            <option value="Leung TK">梁子琪 (Leung TK)</option>
                            <option value="Lui WM">雷慧敏 (Lui WM)</option>
                            <option value="Mak CS">麥正沁 (Mak CS)</option>
                            <option value="Tsang LK">曾麗君 (Tsang LK)</option>
                            <option value="Wong CN">黃志能 (Wong CN)</option>
                        </optgroup>
                        <optgroup label="其他協作">
                            <option value="Kung PL">Kung PL</option>
                            <option value="Chan KW">Chan KW</option>
                            <option value="Chan WC">Chan WC</option>
                            <option value="Leung KY">Leung KY</option>
                            <option value="班主任">班主任</option>
                        </optgroup>
                    </select>

                    <select value={viewMode} onChange={(e) => setViewMode(e.target.value)} className="bg-teal-600 text-white font-medium border border-teal-700 text-sm rounded-lg focus:ring-teal-500 p-2 md:p-2.5 shadow-sm cursor-pointer hover:bg-teal-700">
                        <option value="calendar">📅 年曆視角 (按月)</option>
                        <option value="list">📝 編輯列表 (實時修改)</option>
                    </select>
                </div>
            </div>

            <div className="p-4 md:p-6 bg-gray-50 min-h-[500px]">
                {loading ? (
                    <div className="flex flex-col items-center justify-center h-64 text-teal-600">
                        <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-teal-600 mb-4"></div>
                        <p>正在同步實時資料庫...</p>
                    </div>
                ) : (
                    <>
                        {viewMode === 'calendar' && <CalendarView tasks={filteredTasks} />}
                        {viewMode === 'list' && <ListView tasks={filteredTasks} editingTask={editingTask} setEditingTask={setEditingTask} handleSave={handleSave} />}
                    </>
                )}
            </div>
        </div>
    );
}

// --- 視圖: 年曆 ---
function CalendarView({ tasks }) {
    const academicMonths = [
        { num: 9, name: "九月 (Sep)" }, { num: 10, name: "十月 (Oct)" }, { num: 11, name: "十一月 (Nov)" },
        { num: 12, name: "十二月 (Dec)" }, { num: 1, name: "一月 (Jan)" }, { num: 2, name: "二月 (Feb)" },
        { num: 3, name: "三月 (Mar)" }, { num: 4, name: "四月 (Apr)" }, { num: 5, name: "五月 (May)" },
        { num: 6, name: "六月 (Jun)" }, { num: 7, name: "七月 (Jul)" }, { num: 8, name: "八月 (Aug)" }
    ];

    const getCategoryStyle = (category) => {
        switch(category) {
            case '學校關注事項': return 'bg-emerald-50 text-emerald-800 border-emerald-300 hover:bg-emerald-100';
            case '組本關注事項': return 'bg-indigo-50 text-indigo-800 border-indigo-300 hover:bg-indigo-100';
            case '恆常工作項目': return 'bg-slate-50 text-slate-800 border-slate-300 hover:bg-slate-100';
            default: return 'bg-gray-50 text-gray-800 border-gray-300';
        }
    };

    return (
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
            {academicMonths.map(m => {
                const monthTasks = tasks.filter(t => t.month === m.num);
                let headerColor = "bg-slate-700";
                if(m.num >= 9 && m.num <= 12) headerColor = "bg-teal-700";
                if(m.num >= 1 && m.num <= 6) headerColor = "bg-indigo-700";

                return (
                    <div key={m.num} className="bg-white rounded-lg border border-gray-200 shadow-sm flex flex-col h-full overflow-hidden">
                        <div className={`${headerColor} text-white font-bold py-2.5 px-4 text-center tracking-wider flex justify-between items-center`}>
                            <span>{m.name}</span>
                            <span className="bg-white/20 text-xs px-2 py-1 rounded-full">{monthTasks.length} 項</span>
                        </div>
                        <div className="p-3 flex-1 bg-gray-50/50">
                            {monthTasks.length > 0 ? (
                                monthTasks.map(t => (
                                    <div key={t.dbId} className={`mb-3 p-3 rounded-md border ${getCategoryStyle(t.category)} relative group cursor-default transition-all duration-200`}>
                                        <div className="text-xs font-semibold mb-1 opacity-70">{t.category}</div>
                                        <div className="font-bold mb-2 leading-tight">{t.taskId} - {t.name}</div>
                                        <div className="text-xs text-gray-700 bg-white/60 p-1.5 rounded flex items-start">
                                            <UserPen className="w-3 h-3 mr-1 mt-0.5 flex-shrink-0" />
                                            <span>{t.pic ? t.pic.join(', ') : '未指派'}</span>
                                        </div>
                                    </div>
                                ))
                            ) : (
                                <div className="text-gray-400 text-sm flex flex-col items-center justify-center py-8">
                                    無排定任務
                                </div>
                            )}
                        </div>
                    </div>
                );
            })}
        </div>
    );
}

// --- 視圖: 列表 (可編輯) ---
function ListView({ tasks, editingTask, setEditingTask, handleSave }) {
    if (tasks.length === 0) {
        return (
            <div className="text-center py-16 bg-white rounded-lg border border-dashed border-gray-300">
                <ClipboardList className="w-16 h-16 text-gray-300 mx-auto mb-4" />
                <p className="text-gray-500 text-lg">找不到符合條件的任務</p>
            </div>
        );
    }

    const sortedTasks = [...tasks].sort((a, b) => {
        const mapMonth = (m) => m >= 9 ? m - 9 : m + 3;
        return mapMonth(a.month) - mapMonth(b.month);
    });

    const getCategoryBadge = (category) => {
        switch(category) {
            case '學校關注事項': return 'bg-emerald-100 text-emerald-800 border-emerald-200';
            case '組本關注事項': return 'bg-indigo-100 text-indigo-800 border-indigo-200';
            case '恆常工作項目': return 'bg-slate-200 text-slate-800 border-slate-300';
            default: return 'bg-gray-100 text-gray-800 border-gray-200';
        }
    };

    return (
        <div className="space-y-3">
            {sortedTasks.map(t => {
                const isEditing = editingTask && editingTask.dbId === t.dbId;

                if (isEditing) {
                    return (
                        <div key={t.dbId} className="p-4 md:p-5 rounded-lg border-2 border-teal-400 shadow-lg bg-teal-50 flex flex-col lg:flex-row gap-4">
                            <div className="flex-1 space-y-3">
                                <div className="flex gap-2">
                                    <input 
                                        className="border rounded p-1.5 w-24 font-bold" 
                                        value={editingTask.taskId}
                                        onChange={(e) => setEditingTask({...editingTask, taskId: e.target.value})}
                                        placeholder="編號"
                                    />
                                    <input 
                                        className="border rounded p-1.5 flex-1 font-bold" 
                                        value={editingTask.name}
                                        onChange={(e) => setEditingTask({...editingTask, name: e.target.value})}
                                        placeholder="任務名稱"
                                    />
                                </div>
                                <div className="flex gap-2 text-sm">
                                    <select 
                                        className="border rounded p-1.5"
                                        value={editingTask.category}
                                        onChange={(e) => setEditingTask({...editingTask, category: e.target.value})}
                                    >
                                        <option value="學校關注事項">學校關注事項</option>
                                        <option value="組本關注事項">組本關注事項</option>
                                        <option value="恆常工作項目">恆常工作項目</option>
                                    </select>
                                    <select 
                                        className="border rounded p-1.5"
                                        value={editingTask.month}
                                        onChange={(e) => setEditingTask({...editingTask, month: e.target.value})}
                                    >
                                        {[9,10,11,12,1,2,3,4,5,6,7,8].map(m => <option key={m} value={m}>{m}月</option>)}
                                    </select>
                                </div>
                                <div>
                                    <input 
                                        className="border rounded p-1.5 w-full text-sm" 
                                        value={editingTask.pic ? editingTask.pic.join(', ') : ''}
                                        onChange={(e) => setEditingTask({...editingTask, pic: e.target.value.split(',').map(s=>s.trim())})}
                                        placeholder="負責人 (以逗號分隔)"
                                    />
                                </div>
                            </div>
                            <div className="flex flex-row lg:flex-col justify-end gap-2">
                                <button onClick={() => handleSave(editingTask)} className="bg-teal-600 text-white px-4 py-2 rounded shadow hover:bg-teal-700 flex items-center">
                                    <Check className="w-4 h-4 mr-1"/> 儲存更新
                                </button>
                                <button onClick={() => setEditingTask(null)} className="bg-gray-300 text-gray-700 px-4 py-2 rounded shadow hover:bg-gray-400 flex items-center">
                                    <X className="w-4 h-4 mr-1"/> 取消
                                </button>
                            </div>
                        </div>
                    );
                }

                return (
                    <div key={t.dbId} className={`p-4 md:p-5 rounded-lg border border-gray-200 shadow-sm flex flex-col lg:flex-row lg:items-center justify-between transition hover:shadow-md bg-white border-l-4 hover:border-l-teal-500`}>
                        <div className="mb-4 lg:mb-0 lg:w-3/5 pr-4">
                            <div className="flex items-center gap-2 mb-2">
                                <span className={`inline-block px-2.5 py-1 text-xs rounded border font-semibold ${getCategoryBadge(t.category)}`}>{t.category}</span>
                                <span className="text-xs text-gray-500 bg-gray-100 px-2 py-1 rounded flex items-center"><CalendarIcon className="w-3 h-3 mr-1"/>預計 {t.month} 月</span>
                            </div>
                            <h4 className="font-bold text-gray-800 text-lg leading-tight mb-1">
                                <span className="text-teal-700 mr-2">{t.taskId}</span>{t.name}
                            </h4>
                        </div>
                        <div className="text-sm lg:w-2/5 flex flex-col sm:flex-row gap-3 items-start sm:items-center justify-between bg-gray-50 p-3 rounded-lg border border-gray-100">
                            <div>
                                <div className="font-medium text-gray-500 mb-1.5 text-xs uppercase tracking-wide flex items-center"><Users className="w-3 h-3 mr-1"/>負責與協助人員</div>
                                <div className="flex flex-wrap gap-1.5">
                                    {t.pic && t.pic.map((p, idx) => {
                                        const badgeClass = p === '所有成員' ? 'bg-yellow-100 text-yellow-800 border-yellow-200' : 'bg-blue-50 text-blue-700 border-blue-200';
                                        return <span key={idx} className={`${badgeClass} px-2 py-1 rounded border text-xs font-medium`}>{p}</span>;
                                    })}
                                </div>
                            </div>
                            <button 
                                onClick={() => setEditingTask(t)}
                                className="text-teal-600 hover:text-teal-800 hover:bg-teal-100 p-2 rounded-full transition"
                                title="編輯任務"
                            >
                                <Edit2 className="w-5 h-5" />
                            </button>
                        </div>
                    </div>
                );
            })}
        </div>
    );
}


// ==========================================
// 分頁: 核心小組成員 (靜態)
// ==========================================
function MembersTab() {
    return (
        <div className="bg-white rounded-xl shadow-md p-6 md:p-8 mb-6 border-t-4 border-blue-600 animation-fade-in">
            <h2 className="text-2xl font-bold text-gray-800 mb-6 flex items-center">
                <Users className="text-blue-600 mr-3 w-8 h-8" /> 核心小組架構
            </h2>
            
            <div className="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8">
                <div className="bg-blue-50 p-6 rounded-xl border border-blue-100 text-center flex flex-col items-center justify-center">
                    <div className="w-16 h-16 bg-blue-600 text-white rounded-full flex items-center justify-center text-2xl font-bold mb-3 shadow-md">黃</div>
                    <h3 className="text-xl font-bold text-blue-900">黃文禮老師 (Wong ML)</h3>
                    <p className="text-blue-700 font-medium mt-1 px-3 py-1 bg-blue-100 rounded-full text-sm">統籌</p>
                </div>
                <div className="bg-blue-50 p-6 rounded-xl border border-blue-100 text-center flex flex-col items-center justify-center">
                    <div className="w-16 h-16 bg-blue-500 text-white rounded-full flex items-center justify-center text-2xl font-bold mb-3 shadow-md">鄭</div>
                    <h3 className="text-xl font-bold text-blue-900">鄭鍵鴻老師 (Cheng KH)</h3>
                    <p className="text-blue-700 font-medium mt-1 px-3 py-1 bg-blue-100 rounded-full text-sm">副統籌</p>
                </div>
            </div>

            <h3 className="text-lg font-semibold text-gray-700 mb-4 border-b pb-2">小組成員</h3>
            <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
                {['陳家瑩老師 (Chan KY)', '陳麗興老師 (Chan LH)', '周貝兒老師 (Chau PY)', '朱寶兒老師 (Chu PY)', 
                  '何狄燊老師 (Ho TS)', '韓惠錦老師 (Hon WK)', '梁子琪老師 (Leung TK)', '雷慧敏老師 (Lui WM)', 
                  '麥正沁老師 (Mak CS)', '曾麗君老師 (Tsang LK)', '黃志能老師 (Wong CN)'].map(name => (
                    <div key={name} className="flex items-center p-3 bg-gray-50 rounded-lg border border-gray-100"><Users className="text-gray-400 w-4 h-4 mr-2" />{name}</div>
                ))}
            </div>
            
            <div className="mt-8 bg-yellow-50 border-l-4 border-yellow-400 p-4 rounded text-sm text-yellow-800">
                <strong>註：</strong> 以下工作項目中若提及「所有生涯規劃教育組成員」，則指上述核心小組成員。<br/>部分老師如 Kung PL、Chan KW 等雖參與特定工作，未列於核心名單中。
            </div>
        </div>
    );
}

// ==========================================
// 分頁: 學校關注事項 (靜態備查)
// ==========================================
function SchoolTab() {
    return (
        <div className="space-y-6 animation-fade-in">
            <div className="bg-white rounded-xl shadow-md overflow-hidden">
                <div className="bg-emerald-600 px-6 py-4">
                    <h2 className="text-xl font-bold text-white flex items-center"><Target className="mr-2 w-5 h-5" /> 關注事項 2：優化生涯規劃 (Careerization)</h2>
                </div>
                <div className="p-6 grid grid-cols-1 lg:grid-cols-2 gap-4">
                     <StaticCard title="2.1.1 善用校本及外間資源" color="emerald" pic="Kung PL, Wong ML, Chan KW, Cheng KH" desc="(包括賽馬會青年向上流計劃YUP!)" />
                     <StaticCard title="2.2.1 整合校內外實務與工作場景學習" color="emerald" pic="Mak CS [校內], Chau PY [校外], Cheng KH [統籌]" />
                     <StaticCard title="2.2.2 透過活動策劃訓練與反思練習" color="emerald" pic="Cheng KH, Chan KY, Leung TK [拉花], Cheng KH [創業]" />
                     <StaticCard title="2.3.1 優化「我的成長印記」和「成績表360」" color="emerald" pic="Wong ML" />
                     <StaticCard title="2.3.2 推行「能力積木」微證書課程計劃" color="emerald" pic="Wong ML" />
                     <StaticCard title="2.3.4 透過多元活動，讓家長了解多元出路" color="emerald" pic="Chan LH" />
                </div>
            </div>
            <div className="bg-white rounded-xl shadow-md overflow-hidden">
                <div className="bg-teal-600 px-6 py-4">
                    <h2 className="text-xl font-bold text-white flex items-center"><Target className="mr-2 w-5 h-5" /> 關注事項 3：擴展關愛文化 (Compassion)</h2>
                </div>
                <div className="p-6 grid grid-cols-1 lg:grid-cols-2 gap-4">
                    <StaticCard title="3.2.1 安老業系列活動" color="teal" pic="Mak CS (負責), Tsang LK (協助)" desc="我校長者學苑聯絡 (Generation 和香港職業發展服務處)" />
                </div>
            </div>
        </div>
    );
}

// ==========================================
// 分頁: 組本關注事項 (靜態備查)
// ==========================================
function CommitteeTab() {
    return (
        <div className="space-y-6 animation-fade-in">
            <div className="bg-white rounded-xl shadow-md overflow-hidden">
                <div className="bg-indigo-600 px-6 py-4">
                    <h2 className="text-xl font-bold text-white flex items-center"><Target className="mr-2 w-5 h-5" /> 組本關注事項 1 & 2</h2>
                </div>
                <div className="p-6">
                    <p className="text-gray-500 mb-4"><CalendarIcon className="inline w-4 h-4 mr-1"/> 請切換至「任務儀表板」進行實時編輯與人員指派。</p>
                    <div className="bg-gray-50 border rounded-lg p-4 text-sm text-gray-700">
                        這裡原本是長長的表格，為了發揮實時協作系統的威力，<strong>組本關注事項 (A1 ~ E1)</strong> 已全數匯入至 <strong>任務儀表板 (第一頁籤)</strong> 中。您可以在那裡直接管理負責人與排程。
                    </div>
                </div>
            </div>
        </div>
    );
}

// ==========================================
// 分頁: 恆常工作 (靜態備查)
// ==========================================
function RoutineTab() {
     return (
        <div className="space-y-6 animation-fade-in">
            <div className="bg-white rounded-xl shadow-md overflow-hidden">
                <div className="bg-slate-700 px-6 py-4">
                    <h2 className="text-xl font-bold text-white flex items-center"><ClipboardList className="mr-2 w-5 h-5" /> 恆常工作項目</h2>
                </div>
                <div className="p-6">
                    <p className="text-gray-500 mb-4"><CalendarIcon className="inline w-4 h-4 mr-1"/> 請切換至「任務儀表板」進行實時編輯與人員指派。</p>
                    <div className="bg-gray-50 border rounded-lg p-4 text-sm text-gray-700">
                        <strong>恆常工作 (ZA ~ ZJ)</strong> 也已全數整合至 <strong>任務儀表板</strong> 的資料庫中。
                    </div>
                </div>
            </div>
        </div>
    );
}

// 小卡片組件 (用於靜態頁面展示)
function StaticCard({ title, color, pic, desc }) {
    return (
        <div className="border rounded-lg p-4 hover:shadow-md transition bg-white">
            <h3 className={`font-bold text-lg text-${color}-800 mb-2`}>{title}</h3>
            {desc && <p className="text-sm text-gray-500 mb-2">{desc}</p>}
            <div className="bg-gray-50 p-2 rounded text-sm text-gray-700 border">
                <span className="font-semibold block mb-1">主要負責/協作：</span>
                {pic}
            </div>
        </div>
    );
}
