[rain_hero.html](https://github.com/user-attachments/files/27856421/rain_hero.html)
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>暴雨小英雄：超級任務</title>
    <!-- 引入 React, TailwindCSS 與字型 -->
    <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=ZCOOL+KuaiLe&display=swap" rel="stylesheet">
    
    <style>
        /* 統一全遊戲字體，強化童趣卡通感 */
        body, button, div, h1, h2, h3 {
            font-family: 'ZCOOL KuaiLe', 'Noto Sans TC', sans-serif;
        }
        body { 
            background: #5c94fc; 
            margin: 0; 
            overflow: hidden; 
            user-select: none;
        }
        /* 復古卡通大富翁地圖背景 */
        .world-map { 
            background: #4ab44a; 
            background-image: radial-gradient(#3d9e3d 25%, transparent 25%);
            background-size: 30px 30px;
            position: relative;
            box-shadow: inset 0 0 40px rgba(0,0,0,0.3);
        }
        /* 平滑的角色移動動畫 */
        .player-sprite { 
            transition: all 0.5s cubic-bezier(0.25, 1, 0.5, 1); 
        }
        @keyframes floating { 
            0%, 100% { transform: translateY(0) scale(1); } 
            50% { transform: translateY(-8px) scale(1.05); } 
        }
        .float-hero { animation: floating 2s infinite ease-in-out; }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useRef } = React;

        // --- 核心資料庫 (融合所有降雨與安全核心知識) ---
        const CHARS = [
            { id: 1, icon: '👦', name: '小健' }, { id: 2, icon: '👧', name: '小晴' },
            { id: 3, icon: '🐱', name: '波波' }, { id: 4, icon: '🤖', name: '雷達兵' }
        ];

        const GEARS = [
            { id: 1, icon: '👢', name: '防護雨鞋', desc: '涉水不怕斜坡濕滑' },
            { id: 2, icon: '🦺', name: '亮亮雨衣', desc: '提高雨天視線可見度' },
            { id: 3, icon: '☂️', name: '神奇雨傘', desc: '抵擋強風與折射盲區' }
        ];

        const QUIZZES = [
            { q: "定義上，氣象測站測得多少降雨量才算「下雨」？", a: "0.1 毫米 (mm) 或以上", options: ["0.1 毫米 (mm) 或以上", "1.0 毫米 (mm)", "5.0 毫米 (mm)"], fact: "只要氣象局測站測到 0.1mm 以上，在統計上就視為有降雨喔！" },
            { q: "降雨機率 100% 代表什麼意思？", a: "降雨機率大於 95%", options: ["絕對會下大暴雨", "降雨機率大於 95%", "全台灣都在下雨"], fact: "氣象預報的 100% 是四捨五入的結果，代表機率大於 95%。" },
            { q: "降雨機率 0% 的日子，還會下雨嗎？", a: "仍有 1% - 4% 的機率會下雨", options: ["絕對不會下雨", "仍有 1% - 4% 的機率會下雨", "天空會變黑"], fact: "0% 也是四捨五入的結果，代表機率小於 5%，但還是有極低機會降雨！" },
            { q: "降雨機率 70% 代表什麼？", a: "該地區有 70% 的機會下雨", options: ["有 70% 的面積在下雨", "雨會下得比平常大 70%", "該地區有 70% 的機會下雨"], fact: "降雨機率是指「可能性」，跟雨下得大不大、範圍多大沒有關係喔！" },
            { q: "24 小時累積雨量達多少毫米 (mm) 以上稱為「大雨」？", a: "80 mm", options: ["50 mm", "80 mm", "200 mm"], fact: "大雨的定義是 24 小時累積雨量達 80mm 以上，或時雨量達 40mm 以上。" },
            { q: "24 小時累積雨量達多少毫米 (mm) 以上稱為「豪雨」？", a: "200 mm", options: ["100 mm", "200 mm", "500 mm"], fact: "豪雨累積雨量達 200mm 以上（若達 350mm 則為大豪雨，500mm 為超大豪雨）。" },
            { q: "雨天走在路上，為什麼要避開變電箱與鐵製水溝蓋？", a: "可能有漏電與濕滑跌倒危險", options: ["會被吸進去", "可能有漏電與濕滑跌倒危險", "水溝蓋會發燙"], fact: "積水可能導電，且雨天金屬表面與斜坡極度濕滑，視線死角也多！" },
            { q: "雨天開車或過馬路時，水坑會帶來什麼安全隱憂？", a: "光線折射讓人誤判水深與盲區", options: ["水坑會產生磁場", "光線折射讓人誤判水深與盲區", "輪胎會融化"], fact: "積水會產生光線折射，使人無法看清真實深度，容易踩空受困。" }
        ];

        function App() {
            const [view, setView] = useState('START');
            const [player, setPlayer] = useState({ char: null, gear: null, level: 0, score: 0 });
            const [diceValue, setDiceValue] = useState(null);
            const [isRolling, setIsRolling] = useState(false);
            const [feedback, setFeedback] = useState(null);
            const [showQuiz, setShowQuiz] = useState(false);

            // --- 小遊戲狀態 ---
            const [gameScore, setGameScore] = useState(0);
            const [gameTimer, setGameTimer] = useState(0);

            // 1. 別踩白塊（大水滴音樂遊戲）
            const [pianoRows, setPianoRows] = useState([]);
            // 2. 敲地鼠
            const [moles, setMoles] = useState(Array(6).fill(false));
            // 3. 踩地雷 (1=安全, 9=地雷, 0=已翻開)
            const [mineGrid, setMineGrid] = useState([]);

            // --- 地圖精準格子座標 (8個關卡，圍繞成童趣的蛇形大富翁路線) ---
            const MAP_CELLS = [
                { x: 50,  y: 280, name: "起點" },
                { x: 160, y: 160, name: "下雨定義" },
                { x: 300, y: 70,  name: "降雨機率" },
                { x: 450, y: 70,  name: "0%與100%" },
                { x: 580, y: 160, name: "大雨定義" },
                { x: 680, y: 280, name: "豪雨定義" },
                { x: 500, y: 350, name: "雨天安全" },
                { x: 280, y: 350, name: "終點任務" }
            ];

            // 獲取當前玩家在畫布上的實際置中座標 (修正浮在字上或偏離的問題)
            const getPlayerPos = () => {
                const cell = MAP_CELLS[player.level] || MAP_CELLS[0];
                return { x: cell.x + 8, y: cell.y - 25 }; 
            };

            // --- 骰子與前進邏輯 ---
            const rollDice = () => {
                if (isRolling || showQuiz) return;
                setIsRolling(true);
                setDiceValue(null);
                
                let counter = 0;
                const interval = setInterval(() => {
                    setDiceValue(Math.floor(Math.random() * 3) + 1); // 每次前進 1~3 格
                    counter++;
                    if (counter > 8) {
                        clearInterval(interval);
                        setIsRolling(false);
                        setShowQuiz(true); 
                    }
                }, 100);
            };

            const handleAnswer = (selectedOpt) => {
                const currentQuiz = QUIZZES[player.level % QUIZZES.length];
                if (selectedOpt === currentQuiz.a) {
                    setFeedback({ success: true, fact: currentQuiz.fact });
                    setPlayer(p => ({ ...p, score: p.score + 200 }));
                } else {
                    setFeedback({ success: false, fact: currentQuiz.fact });
                }
            };

            const confirmFeedback = () => {
                const isCorrect = feedback.success;
                setFeedback(null);
                setShowQuiz(false);

                if (isCorrect) {
                    // 答對了，根據剛才的骰子點數前進
                    const steps = diceValue || 1;
                    let nextLevel = player.level + steps;
                    if (nextLevel >= MAP_CELLS.length - 1) {
                        nextLevel = MAP_CELLS.length - 1; // 抵達終點
                        setPlayer(p => ({ ...p, level: nextLevel }));
                        setTimeout(() => triggerMiniGame(), 600);
                    } else {
                        setPlayer(p => ({ ...p, level: nextLevel }));
                        // 隨機觸發中間的小遊戲關卡
                        if (Math.random() > 0.4) {
                            setTimeout(() => triggerMiniGame(), 600);
                        }
                    }
                } else {
                    // 答錯原地踏步，不前進
                    setDiceValue(null);
                }
            };

            // 隨機抽選小遊戲
            const triggerMiniGame = () => {
                const games = ['PIANO', 'MOLE', 'MINE'];
                const chosen = games[Math.floor(Math.random() * games.length)];
                setGameScore(0);
                
                if (chosen === 'PIANO') {
                    setGameTimer(15);
                    // 初始化別採白塊資料：4行，每行4格，只有一格是「藍色大水滴(2)」
                    const initialRows = Array.from({length: 4}, () => {
                        const row = [0, 0, 0, 0];
                        row[Math.floor(Math.random() * 4)] = 2; // 2 代表有大水滴
                        return row;
                    });
                    setPianoRows(initialRows);
                    setView('GAME_PIANO');
                } else if (chosen === 'MOLE') {
                    setGameTimer(15);
                    setView('GAME_MOLE');
                } else if (chosen === 'MINE') {
                    // 初始化踩地雷：4x4 網格，放置3個烏雲地雷
                    let grid = Array(16).fill(1); 
                    let count = 0;
                    while(count < 3) {
                        let idx = Math.floor(Math.random() * 16);
                        if(grid[idx] !== 9) { grid[idx] = 9; count++; }
                    }
                    setMineGrid(grid);
                    setView('GAME_MINE');
                }
            };

            // --- 小遊戲計時器與邏輯 ---
            useEffect(() => {
                if (view.startsWith('GAME_') && view !== 'GAME_MINE') {
                    const timer = setInterval(() => {
                        setGameTimer(t => {
                            if (t <= 1) {
                                clearInterval(timer);
                                // 時間到，結算分數
                                setPlayer(p => ({ ...p, score: p.score + gameScore * 10 }));
                                alert(`遊戲結束！你成功獲得了 ${gameScore * 10} 積分！`);
                                setView(player.level >= MAP_CELLS.length - 1 ? 'END' : 'MAP');
                                return 0;
                            }
                            return t - 1;
                        });
                    }, 1000);
                    return () => clearInterval(timer);
                }
            }, [view, gameScore]);

            // 別踩白塊點擊
            const handlePianoClick = (rowIndex, colIndex, val) => {
                if (rowIndex === 3 && val === 2) { // 只能點擊最底下一行的藍色水滴
                    setGameScore(s => s + 1);
                    // 整體下移一行，並在頂部生成新的一行
                    setPianoRows(prev => {
                        const next = [...prev];
                        next.pop();
                        const newRow = [0,0,0,0];
                        newRow[Math.floor(Math.random() * 4)] = 2;
                        next.unshift(newRow);
                        return next;
                    });
                }
            };

            // 敲地鼠打擊
            const hitMole = (idx) => {
                if (moles[idx]) {
                    setGameScore(s => s + 1);
                    setMoles(prev => {
                        const next = [...prev];
                        next[idx] = false;
                        return next;
                    });
                }
            };

            // 地鼠隨機冒出
            useEffect(() => {
                if (view === 'GAME_MOLE') {
                    const moleInterval = setInterval(() => {
                        setMoles(prev => {
                            const next = [...prev];
                            const randomIdx = Math.floor(Math.random() * 6);
                            next[randomIdx] = Math.random() > 0.3; // 隨機出現或消失
                            return next;
                        });
                    }, 600);
                    return () => clearInterval(moleInterval);
                }
            }, [view]);

            // 踩地雷點擊
            const clickMineCell = (idx) => {
                if (mineGrid[idx] === 9) {
                    alert("💥 踩到烏雲閃電雷擊了！扣除 100 積分，安全撤退！");
                    setPlayer(p => ({ ...p, score: Math.max(0, p.score - 100) }));
                    setView(player.level >= MAP_CELLS.length - 1 ? 'END' : 'MAP');
                } else if (mineGrid[idx] === 1) {
                    setGameScore(s => s + 1);
                    setMineGrid(prev => {
                        const next = [...prev];
                        next[idx] = 0; // 0 代表已安全翻開
                        return next;
                    });
                    // 如果翻開所有安全格子(13個)則獲勝
                    if (gameScore + 1 === 13) {
                        alert("🎉 恭喜！成功避開所有雷區，獲得 300 獎勵積分！");
                        setPlayer(p => ({ ...p, score: p.score + 300 }));
                        setView(player.level >= MAP_CELLS.length - 1 ? 'END' : 'MAP');
                    }
                }
            };

            // --- 視圖渲染器 ---
            if (view === 'START') return (
                <div className="h-screen flex flex-col items-center justify-center text-white p-6" style={{ background: 'linear-gradient(to bottom, #4facfe 0%, #00f2fe 100%)' }}>
                    <div className="text-center float-hero">
                        <div className="text-9xl mb-4">⛈️🏃‍♂️</div>
                        <h1 className="text-7xl md:text-8xl font-black text-yellow-300 drop-shadow-[0_6px_0_rgba(0,0,0,0.4)] tracking-wide mb-12">
                            暴雨小英雄<br/><span className="text-4xl text-white block mt-4">超級氣象任務大富翁</span>
                        </h1>
                    </div>
                    <button onClick={() => setView('CHAR')} className="px-14 py-6 bg-amber-400 text-rose-700 rounded-full text-4xl font-bold border-b-[10px] border-amber-700 active:border-b-0 active:translate-y-2 hover:scale-105 transition-all shadow-2xl">
                        開始大冒險 GO!
                    </button>
                </div>
            );

            if (view === 'CHAR' || view === 'GEAR') return (
                <div className="p-8 max-w-4xl mx-auto h-screen flex flex-col justify-center items-center">
                    <h2 className="text-5xl font-black text-white text-center mb-12 drop-shadow-md">
                        {view === 'CHAR' ? '🌟 選擇你的冒險小英雄' : '🎒 裝備你的氣象導航道具'}
                    </h2>
                    <div className="grid grid-cols-2 md:grid-cols-3 gap-8 w-full">
                        {(view === 'CHAR' ? CHARS : GEARS).map(item => (
                            <button key={item.id} onClick={() => {
                                if(view === 'CHAR') { setPlayer({...player, char: item}); setView('GEAR'); }
                                else { setPlayer({...player, gear: item}); setView('MAP'); }
                            }} className="bg-amber-50 p-6 rounded-[2.5rem] border-b-8 border-amber-200 hover:scale-105 hover:border-sky-400 transition-all shadow-xl group">
                                <div className="text-7xl mb-4 group-hover:animate-bounce">{item.icon}</div>
                                <div className="text-3xl font-black text-slate-800 mb-1">{item.name}</div>
                                {item.desc && <div className="text-sm text-slate-500 font-medium">{item.desc}</div>}
                            </button>
                        ))}
                    </div>
                </div>
            );

            if (view === 'MAP') return (
                <div className="p-4 flex flex-col items-center h-screen justify-center max-w-5xl mx-auto">
                    {/* 頂部英雄資訊欄 */}
                    <div className="bg-sky-900/50 backdrop-blur-md rounded-3xl p-4 w-full mb-4 flex justify-between items-center text-white border-4 border-white/40 shadow-lg">
                        <div className="flex items-center gap-3">
                            <span className="text-4xl">{player.char?.icon}</span>
                            <div>
                                <div className="text-xl font-bold">{player.char?.name}</div>
                                <div className="text-sm text-yellow-300">道具: {player.gear?.icon} {player.gear?.name}</div>
                            </div>
                        </div>
                        <div className="text-right">
                            <div className="text-xs text-sky-200">🏆 HERO SCORE</div>
                            <div className="text-3xl font-black text-yellow-400">{player.score} <span className="text-sm text-white">分</span></div>
                        </div>
                    </div>

                    {/* 卡通大地圖 */}
                    <div className="world-map w-full h-[460px] rounded-[2.5rem] border-[10px] border-emerald-700 relative overflow-hidden shadow-2xl">
                        {/* 繪製關卡連線 */}
                        <svg className="absolute inset-0 w-full h-full pointer-events-none">
                            <polyline 
                                points={MAP_CELLS.map(c => `${c.x + 28},${c.y + 28}`).join(' ')} 
                                fill="none" stroke="#fff" strokeWidth="8" strokeDasharray="12" opacity="0.7" 
                            />
                        </svg>

                        {/* 渲染所有關卡基地 */}
                        {MAP_CELLS.map((cell, i) => {
                            let statusColor = "bg-slate-400 border-slate-600";
                            if (i < player.level) statusColor = "bg-emerald-400 border-emerald-600 animate-pulse";
                            if (i === player.level) statusColor = "bg-yellow-400 border-yellow-600 ring-4 ring-white";
                            
                            return (
                                <div 
                                    key={i} 
                                    className={`absolute w-14 h-14 rounded-full flex flex-col items-center justify-center border-4 text-white font-black text-lg shadow-md ${statusColor}`}
                                    style={{ left: cell.x, top: cell.y }}
                                >
                                    <span>{i === 0 ? '🏁' : i === MAP_CELLS.length - 1 ? '👑' : i}</span>
                                    <span className="absolute -bottom-6 text-xs text-slate-900 bg-white/80 px-1 rounded font-bold whitespace-nowrap shadow-sm">
                                        {cell.name}
                                    </span>
                                </div>
                            );
                        })}

                        {/* 精準定位的小英雄本體 */}
                        <div 
                            className="absolute player-sprite text-5xl float-hero z-30"
                            style={{ left: getPlayerPos().x, top: getPlayerPos().y }}
                        >
                            {player.char?.icon}
                        </div>
                    </div>

                    {/* 右下角大富翁操作盤 */}
                    <div className="mt-8 flex items-center gap-6 bg-white/20 p-4 rounded-3xl backdrop-blur-sm border border-white/20">
                        <div className="text-center">
                            <div className="text-xs text-white font-bold mb-1">本次骰子點數</div>
                            <div className="w-16 h-16 bg-white rounded-2xl flex items-center justify-center text-4xl font-black text-slate-800 shadow-md border-b-4 border-gray-300">
                                {isRolling ? '🌀' : (diceValue || '?')}
                            </div>
                        </div>
                        <button 
                            onClick={rollDice} 
                            disabled={isRolling || showQuiz}
                            className="px-8 py-5 bg-gradient-to-r from-rose-500 to-orange-500 text-white rounded-2xl text-2xl font-black shadow-lg hover:brightness-110 active:scale-95 transition-all disabled:opacity-50"
                        >
                            🎲 丟骰子前進任務！
                        </button>
                    </div>

                    {/* 答題彈窗 */}
                    {showQuiz && (
                        <div className="fixed inset-0 bg-black/60 backdrop-blur-sm flex items-center justify-center p-4 z-50 animate-fadeIn">
                            <div className="bg-white rounded-[2.5rem] p-6 max-w-md w-full border-t-[12px] border-sky-500 shadow-2xl text-slate-800 relative">
                                {!feedback ? (
                                    <>
                                        <div className="text-xs bg-sky-100 text-sky-700 font-bold px-3 py-1 rounded-full w-max mb-3">
                                            🎯 挑戰關卡：{MAP_CELLS[player.level]?.name}
                                        </div>
                                        <h3 className="text-2xl font-black mb-6 leading-snug">
                                            {QUIZZES[player.level % QUIZZES.length].q}
                                        </h3>
                                        <div className="space-y-3">
                                            {QUIZZES[player.level % QUIZZES.length].options.map((opt, idx) => (
                                                <button 
                                                    key={idx} 
                                                    onClick={() => handleAnswer(opt)}
                                                    className="w-full text-left p-4 rounded-2xl border-2 border-slate-200 hover:border-sky-400 hover:bg-sky-50 font-bold transition-all"
                                                >
                                                    {idx + 1}. {opt}
                                                </button>
                                            ))}
                                        </div>
                                    </>
                                ) : (
                                    <div className="text-center py-4">
                                        <div className="text-6xl mb-4">{feedback.success ? '🎉 正確！' : '❌ 答錯囉'}</div>
                                        <div className="bg-amber-50 p-4 rounded-2xl text-left border-l-4 border-amber-400 mb-6 font-medium text-slate-700">
                                            <strong className="text-amber-800 block mb-1">💡 氣象英雄科普秘笈：</strong>
                                            {feedback.fact}
                                        </div>
                                        <button 
                                            onClick={confirmFeedback}
                                            className="px-8 py-3 bg-sky-500 text-white font-bold rounded-xl shadow-md hover:bg-sky-600 transition-colors"
                                        >
                                            {feedback.success ? `向前進 ${diceValue} 格！` : '待在原地繼續努力'}
                                        </button>
                                    </div>
                                )}
                            </div>
                        </div>
                    )}
                </div>
            );

            // 小遊戲 1: 別採白塊（大水滴落下記樂）
            if (view === 'GAME_PIANO') return (
                <div className="h-screen bg-slate-900 flex flex-col items-center justify-center p-4 text-white">
                    <div className="mb-4 text-center">
                        <h2 className="text-3xl font-black text-cyan-400">💧 搶救墜落大水滴！</h2>
                        <p className="text-sm text-slate-400 mt-1">【規則】只點擊最底下一行的「藍色水滴格」，點錯或點上方都無效！</p>
                        <div className="flex justify-around gap-8 text-xl font-bold mt-2">
                            <div>倒數時間: <span className="text-rose-400 text-2xl">{gameTimer}</span> 秒</div>
                            <div>接住水滴: <span className="text-yellow-400 text-2xl">{gameScore}</span> 個</div>
                        </div>
                    </div>
                    <div className="w-72 bg-slate-800 border-4 border-slate-600 rounded-xl overflow-hidden shadow-2xl">
                        {pianoRows.map((row, rIdx) => (
                            <div key={rIdx} className="flex border-b border-slate-700 h-24">
                                {row.map((val, cIdx) => (
                                    <div 
                                        key={cIdx} 
                                        onClick={() => handlePianoClick(rIdx, cIdx, val)}
                                        className={`flex-1 border-r border-slate-700 transition-colors cursor-pointer flex items-center justify-center text-3xl
                                            ${val === 2 ? 'bg-sky-500 active:bg-sky-400' : 'bg-white hover:bg-gray-100'}`}
                                    >
                                        {val === 2 && (rIdx === 3 ? '💧' : '☁️')}
                                    </div>
                                ))}
                            </div>
                        ))}
                    </div>
                </div>
            );

            // 小遊戲 2: 敲地鼠（積水怪大作戰）
            if (view === 'GAME_MOLE') return (
                <div className="h-screen bg-emerald-900 flex flex-col items-center justify-center p-4 text-white">
                    <div className="mb-6 text-center">
                        <h2 className="text-3xl font-black text-yellow-300">🔨 清除積水淤泥怪！</h2>
                        <p className="text-sm text-emerald-300 mt-1">【規則】看到出現淤泥水怪，立刻點擊消滅牠！</p>
                        <div className="flex justify-around gap-8 text-xl font-bold mt-2">
                            <div>倒數時間: <span className="text-rose-400 text-2xl">{gameTimer}</span> 秒</div>
                            <div>消滅數量: <span className="text-yellow-400 text-2xl">{gameScore}</span> 隻</div>
                        </div>
                    </div>
                    <div className="grid grid-cols-3 gap-6 w-80">
                        {moles.map((isOut, idx) => (
                            <div 
                                key={idx}
                                onClick={() => hitMole(idx)}
                                className="h-24 bg-emerald-950 rounded-full border-4 border-emerald-800 shadow-inner relative overflow-hidden cursor-pointer flex items-center justify-center"
                            >
                                <div className={`text-5xl transition-all duration-100 ${isOut ? 'scale-100 translate-y-0' : 'scale-0 translate-y-10'}`}>
                                    👾
                                </div>
                                <div className="absolute bottom-0 w-full h-4 bg-emerald-800/40"></div>
                            </div>
                        ))}
                    </div>
                </div>
            );

            // 小遊戲 3: 踩地雷（暴風雨雷擊區避難）
            if (view === 'GAME_MINE') return (
                <div className="h-screen bg-violet-950 flex flex-col items-center justify-center p-4 text-white">
                    <div className="mb-4 text-center">
                        <h2 className="text-3xl font-black text-violet-300">⚡ 避開烏雲雷擊區！</h2>
                        <p className="text-sm text-violet-400 mt-1">【規則】翻開安全的晴天格子。如果翻到「⚡ 烏雲雷擊」就踩雷結束囉！</p>
                        <div className="text-xl font-bold mt-2">
                            安全探索進度: <span className="text-yellow-400 text-2xl">{gameScore}</span> / 13
                        </div>
                    </div>
                    <div className="grid grid-cols-4 gap-3 bg-violet-900 p-4 rounded-3xl border-4 border-violet-700 shadow-2xl">
                        {mineGrid.map((val, idx) => (
                            <button
                                key={idx}
                                onClick={() => clickMineCell(idx)}
                                disabled={val === 0}
                                className={`w-16 h-16 rounded-xl font-black text-2xl flex items-center justify-center shadow-md transition-all
                                    ${val === 0 ? 'bg-sky-400 text-white' : 'bg-violet-200 hover:bg-white text-transparent active:scale-95'}`}
                            >
                                {val === 0 ? '☀️' : '❓'}
                            </button>
                        ))}
                    </div>
                </div>
            );

            // 終點完結畫面
            if (view === 'END') return (
                <div className="h-screen flex flex-col items-center justify-center text-white p-6" style={{ background: 'linear-gradient(to top, #111827, #311042)' }}>
                    <div className="text-center animate-bounce text-8xl mb-6">👑🏆🎉</div>
                    <h2 className="text-5xl font-black text-yellow-400 mb-2 drop-shadow-md">恭喜達成超級任務！</h2>
                    <p className="text-xl text-slate-300 max-w-md text-center mb-8 leading-relaxed font-medium">
                        你已成功穿越大富翁氣象圖，收集了全套的大雨、豪雨、降雨機率知識，並通過了動感小遊戲的重重考驗！
                    </p>
                    <div className="bg-white/10 border border-white/20 px-8 py-4 rounded-2xl mb-10 text-center">
                        <div className="text-sm text-slate-400 uppercase tracking-widest">最終榮譽總積分</div>
                        <div className="text-6xl font-black text-emerald-400 mt-1">{player.score} <span className="text-xl text-white">pts</span></div>
                    </div>
                    <button 
                        onClick={() => { setPlayer({ char: null, gear: null, level: 0, score: 0 }); setView('START'); }}
                        className="px-10 py-4 bg-emerald-500 hover:bg-emerald-600 font-bold text-2xl rounded-xl shadow-lg transition-colors"
                    >
                        🔄 再玩一次
                    </button>
                </div>
            );

            return null;
        }

        // 渲染到根節點
        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
