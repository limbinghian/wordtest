<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>極致英語單字閃卡系統</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=Noto+Sans+TC:wght@300;400;500;700;900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', 'Noto Sans TC', sans-serif;
        }
        .perspective {
            perspective: 1200px;
        }
        .preserve-3d {
            transform-style: preserve-3d;
        }
        .backface-hidden {
            backface-visibility: hidden;
            -webkit-backface-visibility: hidden;
        }
        .rotate-y-180 {
            transform: rotateY(180deg);
        }
        .flipped {
            transform: rotateY(180deg);
        }
        /* 美化滾動條 */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col transition-colors duration-300">

    <!-- 導覽列 -->
    <header class="border-b border-slate-800 bg-slate-900/80 backdrop-blur-md sticky top-0 z-50 px-4 py-3">
        <div class="max-w-6xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-4">
            <div class="flex items-center gap-3">
                <div class="p-2 bg-indigo-600 rounded-xl text-white shadow-lg shadow-indigo-500/30">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="m3 9 9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/><polyline points="9 22 9 12 15 12 15 22"/></svg>
                </div>
                <div>
                    <h1 class="text-lg font-black tracking-tight bg-gradient-to-r from-indigo-400 to-cyan-400 bg-clip-text text-transparent">VOCABULARY MASTER</h1>
                    <p class="text-xs text-slate-400">進階英文單字複習系統</p>
                </div>
            </div>
            
            <!-- 模式切換器 -->
            <div class="flex bg-slate-800 p-1 rounded-xl border border-slate-700/50">
                <button onclick="switchMode('flashcard')" id="btn-mode-flashcard" class="px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 bg-indigo-600 text-white shadow">
                    🎴 閃卡複習
                </button>
                <button onclick="switchMode('list')" id="btn-mode-list" class="px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 text-slate-400 hover:text-slate-200">
                    📋 單字清單
                </button>
                <button onclick="switchMode('quiz')" id="btn-mode-quiz" class="px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 text-slate-400 hover:text-slate-200">
                    🎯 挑戰測驗
                </button>
            </div>
        </div>
    </header>

    <!-- 主內容區 -->
    <main class="flex-grow max-w-6xl w-full mx-auto px-4 py-6 flex flex-col justify-start">
        
        <!-- 1. 閃卡模式面板 -->
        <section id="panel-flashcard" class="w-full max-w-4xl mx-auto grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">
            
            <!-- 側邊欄控制（篩選與進度統計） -->
            <div class="lg:col-span-4 bg-slate-800/50 border border-slate-700/60 p-5 rounded-2xl space-y-5">
                <h3 class="font-bold text-slate-300 text-sm tracking-wider uppercase">自訂學習篩選</h3>
                
                <div class="space-y-3">
                    <label class="block">
                        <span class="text-xs text-slate-400 block mb-1">篩選單字狀態</span>
                        <select id="filterSelect" onchange="applyFilters()" class="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-slate-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                            <option value="all">所有單字 (預設)</option>
                            <option value="starred">⭐ 已標記星號</option>
                            <option value="unlearned">📝 尚未標記「已學會」</option>
                            <option value="learned">✅ 已標記「已學會」</option>
                        </select>
                    </label>
                    
                    <div class="flex items-center justify-between text-xs py-1 text-slate-400 border-b border-slate-700/60">
                        <span>目前清單單字數:</span>
                        <span id="stats-current-count" class="font-bold text-indigo-400">0</span>
                    </div>
                </div>

                <div class="pt-2">
                    <h3 class="font-bold text-slate-300 text-sm tracking-wider uppercase mb-3">語音設定</h3>
                    <div class="space-y-3">
                        <div>
                            <div class="flex justify-between text-xs text-slate-400 mb-1">
                                <span>發音速度</span>
                                <span id="val-rate">0.9x</span>
                            </div>
                            <input id="setting-rate" type="range" min="0.5" max="1.5" step="0.1" value="0.9" oninput="updateSpeechSettings()" class="w-full accent-indigo-500 h-1 bg-slate-700 rounded-lg appearance-none cursor-pointer">
                        </div>
                        <div>
                            <span class="text-xs text-slate-400 block mb-1">發音口音</span>
                            <select id="setting-voice" class="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-1.5 text-xs text-slate-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                                <option value="en-US">美式英文 (en-US)</option>
                                <option value="en-GB">英式英文 (en-GB)</option>
                            </select>
                        </div>
                    </div>
                </div>

                <div class="pt-2 border-t border-slate-700/60 space-y-2">
                    <button onclick="shuffleCurrentList()" class="w-full py-2 px-3 bg-slate-700 hover:bg-slate-600 active:scale-95 text-slate-200 rounded-lg text-xs font-semibold flex items-center justify-center gap-2 transition-all">
                        🔀 亂序打亂目前清單
                    </button>
                    <button onclick="resetLearningProgress()" class="w-full py-2 px-3 bg-red-950/40 hover:bg-red-950/60 text-red-300 rounded-lg text-xs font-semibold flex items-center justify-center gap-2 transition-all">
                        🗑️ 重設所有標記進度
                    </button>
                </div>
            </div>

            <!-- 閃卡主展示區 -->
            <div class="lg:col-span-8 space-y-6">
                <!-- 快捷功能與狀態標記 -->
                <div class="flex items-center justify-between">
                    <div class="flex gap-2">
                        <!-- 星號標記 -->
                        <button onclick="toggleCurrentStar()" class="p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700/60 text-slate-300 transition-all active:scale-90 flex items-center gap-2" title="收藏此單字">
                            <svg id="starIcon" xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="transition-transform"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>
                            <span class="text-xs font-medium" id="starTxt">標記星號</span>
                        </button>
                        <!-- 已學會標記 -->
                        <button onclick="toggleCurrentLearned()" class="p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700/60 text-slate-300 transition-all active:scale-90 flex items-center gap-2" title="標記為已學會">
                            <span id="learnedBadge" class="w-3.5 h-3.5 rounded-full border-2 border-slate-500 inline-block"></span>
                            <span class="text-xs font-medium" id="learnedTxt">標記為已學會</span>
                        </button>
                    </div>

                    <!-- 自動播放控制 -->
                    <button onclick="toggleAutoplay()" id="btn-autoplay" class="p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700/60 text-slate-300 transition-all active:scale-90 flex items-center gap-2">
                        <span class="relative flex h-2 w-2 hidden" id="autoplay-indicator">
                          <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
                          <span class="relative inline-flex rounded-full h-2 w-2 bg-emerald-500"></span>
                        </span>
                        <span id="autoplay-icon">▶️</span>
                        <span class="text-xs font-medium" id="autoplay-txt">自動播放</span>
                    </button>
                </div>

                <!-- 閃卡卡片 -->
                <div class="perspective w-full h-80 cursor-pointer">
                    <div id="card" class="preserve-3d duration-500 w-full h-full relative" onclick="toggleFlip()">
                        <!-- 正面 (英文) -->
                        <div class="backface-hidden absolute inset-0 bg-slate-800 border-2 border-slate-700/80 rounded-3xl shadow-2xl flex flex-col items-center justify-between p-8 text-center transition-all hover:border-indigo-500/50">
                            <div class="flex items-center justify-between w-full">
                                <span class="text-indigo-400 text-xs font-bold tracking-widest uppercase bg-indigo-950/80 px-3 py-1 rounded-full border border-indigo-900/40">English</span>
                                <span class="text-slate-500 text-xs font-medium">鍵盤 Space 翻轉</span>
                            </div>
                            
                            <div class="flex flex-col items-center gap-4">
                                <div id="wordDisplay" class="text-3xl sm:text-4xl md:text-5xl font-black text-white tracking-tight break-words max-w-full">載入中...</div>
                                <button onclick="speakWord(event)" class="p-3.5 rounded-full bg-indigo-600/20 hover:bg-indigo-600 text-indigo-400 hover:text-white transition-all transform hover:scale-110 shadow-lg shadow-indigo-500/10" title="發音 (快速鍵: V)">
                                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M11 5L6 9H2v6h4l5 4V5z"></path><path d="M19.07 4.93a10 10 0 0 1 0 14.14M15.54 8.46a5 5 0 0 1 0 7.07"></path></svg>
                                </button>
                            </div>
                            
                            <p class="text-slate-500 text-xs font-medium flex items-center gap-1">
                                <svg class="animate-bounce" xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m15 18-6-6 6-6"/></svg>
                                點擊任意處翻轉查看中文翻譯
                            </p>
                        </div>
                        <!-- 背面 (中文) -->
                        <div class="backface-hidden rotate-y-180 absolute inset-0 bg-gradient-to-br from-indigo-950 to-slate-900 border-2 border-indigo-500/40 rounded-3xl shadow-2xl flex flex-col items-center justify-between p-8 text-center">
                            <div class="flex items-center justify-between w-full">
                                <span class="text-emerald-400 text-xs font-bold tracking-widest uppercase bg-emerald-950/80 px-3 py-1 rounded-full border border-emerald-900/40">中文定義</span>
                                <span class="text-slate-400 text-xs font-medium">鍵盤 Space 翻轉</span>
                            </div>
                            
                            <div class="w-full max-h-40 overflow-y-auto px-4 py-2">
                                <div id="meaningDisplay" class="text-xl sm:text-2xl font-bold text-indigo-100 leading-relaxed whitespace-pre-line">載入中...</div>
                            </div>
                            
                            <p class="text-slate-500 text-xs font-medium flex items-center gap-1">
                                再次點擊卡面翻回正面
                            </p>
                        </div>
                    </div>
                </div>

                <!-- 控制與前進按鈕 -->
                <div class="flex items-center justify-between gap-4">
                    <button onclick="prevCard()" class="flex-1 bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700/60 font-bold py-4 px-6 rounded-2xl shadow-lg transition-all active:scale-95 flex items-center justify-center gap-2">
                        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="19" y1="12" x2="5" y2="12"></line><polyline points="12 19 5 12 12 5"></polyline></svg>
                        上一張 (←)
                    </button>
                    <button onclick="nextCard()" class="flex-1 bg-gradient-to-r from-indigo-600 to-indigo-700 hover:from-indigo-500 hover:to-indigo-600 text-white font-bold py-4 px-6 rounded-2xl shadow-xl shadow-indigo-600/10 transition-all active:scale-95 flex items-center justify-center gap-2">
                        下一張 (→)
                        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="5" y1="12" x2="19" y2="12"></line><polyline points="12 5 19 12 12 19"></polyline></svg>
                    </button>
                </div>

                <!-- 進度條 -->
                <div class="bg-slate-800/40 border border-slate-800 p-4 rounded-2xl">
                    <div class="flex items-center justify-between text-xs text-slate-400 mb-2">
                        <span class="font-medium">清單複習進度</span>
                        <span id="progressIndicator" class="font-bold text-slate-200">0 / 0</span>
                    </div>
                    <div class="w-full bg-slate-800 h-2.5 rounded-full overflow-hidden">
                        <div id="progressBar" class="bg-gradient-to-r from-indigo-500 to-cyan-500 h-full transition-all duration-300" style="width: 0%"></div>
                    </div>
                </div>
            </div>
        </section>

        <!-- 2. 單字清單模式面板 -->
        <section id="panel-list" class="hidden w-full max-w-5xl mx-auto space-y-6">
            <!-- 搜尋與快速統計 -->
            <div class="bg-slate-800/60 border border-slate-700/60 p-4 rounded-2xl flex flex-col md:flex-row gap-4 items-center justify-between">
                <div class="relative w-full md:max-w-md">
                    <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"></circle><line x1="21" y1="21" x2="16.65" y2="16.65"></line></svg>
                    </span>
                    <input type="text" id="listSearch" oninput="renderWordList()" placeholder="搜尋英文單字、片語或中文含意..." class="w-full bg-slate-900 border border-slate-700 rounded-xl pl-10 pr-4 py-2.5 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 text-slate-100 placeholder-slate-500">
                </div>

                <div class="flex gap-4 text-xs font-semibold text-slate-400">
                    <div class="bg-slate-900/60 px-4 py-2 rounded-xl border border-slate-800">
                        ⭐ 星號收藏: <span id="list-stats-starred" class="text-amber-400 ml-1">0</span>
                    </div>
                    <div class="bg-slate-900/60 px-4 py-2 rounded-xl border border-slate-800">
                        ✅ 已學會: <span id="list-stats-learned" class="text-emerald-400 ml-1">0</span>
                    </div>
                </div>
            </div>

            <!-- 清單表格 -->
            <div class="bg-slate-800/30 border border-slate-800 rounded-3xl overflow-hidden shadow-xl">
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-slate-800/80 border-b border-slate-700/60 text-xs font-bold uppercase tracking-wider text-slate-400">
                                <th class="py-4 px-6 w-16 text-center">操作</th>
                                <th class="py-4 px-6 w-1/3">英文單字 / 片語</th>
                                <th class="py-4 px-6 w-1/2">中文定義與詞性</th>
                                <th class="py-4 px-6 w-24 text-center">狀態</th>
                            </tr>
                        </thead>
                        <tbody id="vocabulary-table-body" class="divide-y divide-slate-800/60 text-sm">
                            <!-- JS 動態生成 -->
                        </tbody>
                    </table>
                </div>
                <div id="list-empty-msg" class="hidden text-center py-12 text-slate-500">
                    找不到符合搜尋條件的單字
                </div>
            </div>
        </section>

        <!-- 3. 測驗模式面板 -->
        <section id="panel-quiz" class="hidden w-full max-w-2xl mx-auto">
            <!-- 測驗設定與準備畫面 -->
            <div id="quiz-intro-card" class="bg-gradient-to-br from-slate-800 to-slate-900 border border-slate-700/80 rounded-3xl shadow-2xl p-8 text-center space-y-6">
                <div class="w-20 h-20 bg-indigo-600/20 text-indigo-400 rounded-2xl flex items-center justify-center mx-auto shadow-lg shadow-indigo-500/5">
                    <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><polyline points="12 6 12 12 16 14"></polyline></svg>
                </div>
                <div>
                    <h2 class="text-2xl font-black text-white tracking-tight">單字實力大考驗</h2>
                    <p class="text-sm text-slate-400 mt-2 max-w-md mx-auto">系統將為您隨機抽選 10 題進行「英文 -> 中文」四選一單字選擇題。挑戰看看您的學習成效吧！</p>
                </div>

                <div class="bg-slate-900/60 p-4 rounded-2xl border border-slate-800/80 max-w-xs mx-auto text-left space-y-3">
                    <label class="block">
                        <span class="text-xs text-slate-400 block mb-1">測驗範圍</span>
                        <select id="quizScope" class="w-full bg-slate-800 border border-slate-700 rounded-lg px-3 py-1.5 text-xs text-slate-200">
                            <option value="all">所有 90+ 個單字 (預設)</option>
                            <option value="starred">僅測驗「⭐ 星號收藏」單字</option>
                        </select>
                    </label>
                </div>

                <button onclick="startQuiz()" class="px-8 py-3.5 bg-gradient-to-r from-indigo-500 to-indigo-600 hover:from-indigo-400 hover:to-indigo-500 text-white font-bold rounded-2xl transition-all shadow-lg active:scale-95 text-base inline-block">
                    🏁 開始挑戰
                </button>
            </div>

            <!-- 測驗進行中畫面 -->
            <div id="quiz-play-card" class="hidden bg-slate-800/80 border border-slate-700/60 rounded-3xl p-6 sm:p-8 space-y-6 shadow-2xl">
                <!-- 測驗進度與計分 -->
                <div class="flex items-center justify-between">
                    <span class="text-xs bg-indigo-950 text-indigo-300 font-bold px-3 py-1.5 rounded-full border border-indigo-900/50">
                        第 <span id="quiz-current-idx">1</span> / 10 題
                    </span>
                    <span class="text-xs text-slate-400 font-bold">
                        目前得分：<span id="quiz-score" class="text-emerald-400">0</span>
                    </span>
                </div>

                <!-- 題目 -->
                <div class="text-center py-6 bg-slate-900/40 rounded-2xl border border-slate-800/50 space-y-3 relative">
                    <span class="text-xs text-slate-500 tracking-wider font-semibold uppercase block">請問此單字的定義是？</span>
                    <h3 id="quiz-question-word" class="text-3xl sm:text-4xl font-black text-white tracking-tight px-4 break-words">Word</h3>
                    <button onclick="speakQuizWord()" class="p-2 rounded-full bg-slate-800 hover:bg-slate-700 text-indigo-400 transition-all shadow-md inline-block mt-2" title="聽發音">
                        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M11 5L6 9H2v6h4l5 4V5z"></path><path d="M19.07 4.93a10 10 0 0 1 0 14.14M15.54 8.46a5 5 0 0 1 0 7.07"></path></svg>
                    </button>
                </div>

                <!-- 四個選項 -->
                <div class="grid grid-cols-1 gap-3.5" id="quiz-options-container">
                    <!-- JS 動態生成按鈕 -->
                </div>

                <div class="flex justify-end pt-2">
                    <button id="btn-quiz-next" onclick="nextQuizQuestion()" disabled class="px-6 py-3 bg-indigo-600 disabled:bg-slate-700 disabled:opacity-50 text-white font-bold rounded-xl transition-all shadow-md flex items-center gap-1">
                        下一題
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"></polyline></svg>
                    </button>
                </div>
            </div>

            <!-- 測驗結算畫面 -->
            <div id="quiz-result-card" class="hidden bg-slate-800/80 border border-slate-700/60 rounded-3xl p-8 text-center space-y-6 shadow-2xl">
                <div class="w-24 h-24 bg-emerald-500/10 text-emerald-400 rounded-full flex items-center justify-center mx-auto shadow-lg shadow-emerald-500/5 border border-emerald-500/20">
                    <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path><polyline points="22 4 12 14.01 9 11.01"></polyline></svg>
                </div>
                
                <div>
                    <h2 class="text-2xl font-black text-white">測驗挑戰完成！</h2>
                    <p class="text-sm text-slate-400 mt-2">恭喜您完成了本次的單字複習測試</p>
                </div>

                <div class="grid grid-cols-2 gap-4 max-w-md mx-auto py-4">
                    <div class="bg-slate-900/60 p-4 rounded-2xl border border-slate-800/80">
                        <span class="text-xs text-slate-500 block font-semibold mb-1">最終答對率</span>
                        <span id="quiz-res-percent" class="text-3xl font-black text-indigo-400">0%</span>
                    </div>
                    <div class="bg-slate-900/60 p-4 rounded-2xl border border-slate-800/80">
                        <span class="text-xs text-slate-500 block font-semibold mb-1">實得總分數</span>
                        <span id="quiz-res-score" class="text-3xl font-black text-emerald-400">0</span>
                    </div>
                </div>

                <div class="flex gap-4 justify-center">
                    <button onclick="backToQuizIntro()" class="px-6 py-3 bg-slate-700 hover:bg-slate-600 text-slate-200 font-bold rounded-xl transition-all">
                        🔄 重新挑戰
                    </button>
                    <button onclick="switchMode('flashcard')" class="px-6 py-3 bg-indigo-600 hover:bg-indigo-500 text-white font-bold rounded-xl transition-all shadow-md">
                        🎴 回到閃卡練習
                    </button>
                </div>
            </div>
        </section>

    </main>

    <!-- 底部資訊與快速說明 -->
    <footer class="border-t border-slate-800/60 bg-slate-950/40 py-4 text-center text-xs text-slate-500 px-4">
        <p class="max-w-2xl mx-auto">
            ⌨️ <strong>快捷鍵小提醒（僅閃卡模式支援）：</strong> 
            <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded">←</span> / <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded">→</span> 鍵切換卡片｜
            <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded">Space</span> / <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded">↑</span> / <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded">↓</span> 鍵翻轉卡片｜
            <span class="bg-slate-800 text-slate-300 px-1.5 py-0.5 rounded">V</span> 鍵單字發音。
        </p>
    </footer>

    <script>
        // 資料庫：包含所有 90+ 個單字與片語
        const vocabularyDatabase = [
            { word: "accusation", meaning: "n. [C, U] 指控；指責" },
            { word: "accuse", meaning: "vt. 指控" },
            { word: "addition", meaning: "n. [C] 增加（物）；添加（物）" },
            { word: "additional", meaning: "adj. 附加的；額外的" },
            { word: "additionally", meaning: "adv. 另外；此外" },
            { word: "alcohol", meaning: "n. [U] 酒" },
            { word: "alcoholic", meaning: "n. [C] 酗酒者；酒鬼" },
            { word: "anniversary", meaning: "n. [C] 週年紀念日" },
            { word: "appoint", meaning: "vt. 任命；委任\nvt. 安排（時間、地點）" },
            { word: "appointment", meaning: "n. [C] 預約；約定" },
            { word: "assault", meaning: "vt. 猛烈攻擊；侵犯\nn. [U, C] 傷害罪；攻擊" },
            { word: "ban", meaning: "vt. 明令禁止\nn. [C] 禁令" },
            { word: "bold", meaning: "adj. 大膽的" },
            { word: "certificate", meaning: "n. [C] 證明（書）\nn. [C] 文憑；結業證書" },
            { word: "chaos", meaning: "n. [U] 混亂" },
            { word: "chore", meaning: "n. [C] 日常瑣事；例行工作" },
            { word: "comfort", meaning: "vt. 安慰；撫慰\nn. [U] 安慰；撫慰\nn. [U] 舒服；安逸" },
            { word: "complain", meaning: "vi. vt. 抱怨；投訴" },
            { word: "complaint", meaning: "n. [C] 抱怨；投訴" },
            { word: "domestic", meaning: "adj. 家庭的；家用的\nadj. 國內的" },
            { word: "gap", meaning: "n. [C] 差距；分歧\nn. [C] 間隙" },
            { word: "gender", meaning: "n. [U] 性別" },
            { word: "injustice", meaning: "n. [C, U] 不公正；不公平" },
            { word: "justice", meaning: "n. [U] 正義；公平" },
            { word: "population", meaning: "n. [C, U] （地區、國家的）人口；人口數量" },
            { word: "protest", meaning: "vi. vt. 抗議；反對\nn. [U, C] 抗議；反對" },
            { word: "council", meaning: "n. [C] 委員會；理事會\nn. [C] （市、鎮等）議會" },
            { word: "critic", meaning: "n. [C] 批評家；評論家" },
            { word: "critical", meaning: "adj. 批判性的" },
            { word: "criticism", meaning: "n. [U, C] 批評；責備" },
            { word: "criticize", meaning: "vi. vt. 批評；責備" },
            { word: "decline", meaning: "vi. 減少；衰退\nn. sing., [U] 減少；衰退" },
            { word: "definite", meaning: "adj. 肯定的；確定的" },
            { word: "definitely", meaning: "adv. 當然；確實" },
            { word: "democracy", meaning: "n. [U] 民主" },
            { word: "democratic", meaning: "adj. 民主的" },
            { word: "despair", meaning: "n. [U] 絕望" },
            { word: "despise", meaning: "vt. 鄙視；蔑視" },
            { word: "discriminate", meaning: "vi. 歧視；偏袒\nvi. vt. 辨別；區分" },
            { word: "discrimination", meaning: "n. [U] 歧視；偏袒" },
            { word: "dramatic", meaning: "adj. 突然的；顯著的" },
            { word: "dramatically", meaning: "adv. 突然地；顯著地" },
            { word: "eliminate", meaning: "vt. 消除" },
            { word: "expand", meaning: "vt. vi. 增加；擴大" },
            { word: "expansion", meaning: "n. [C, U] 擴大；增加" },
            { word: "extent", meaning: "n. sing., [U] 程度；限度" },
            { word: "flock", meaning: "vi. 蜂擁；聚集\nn. [C] 一大群" },
            { word: "instinct", meaning: "n. [U, C] 直覺\nn. [U, C] 本能；天性" },
            { word: "instinctive", meaning: "adj. 直覺的" },
            { word: "instinctively", meaning: "adv. 直覺地" },
            { word: "invade", meaning: "vt. 武裝入侵；侵略" },
            { word: "invasion", meaning: "n. [C] 湧入\nn. [C] 武裝入侵；侵略" },
            { word: "jury", meaning: "n. [C] 陪審團" },
            { word: "landlord", meaning: "n. [C] 房東；地主" },
            { word: "license", meaning: "vt. 許可；授權\nn. [C] 許可證；執照" },
            { word: "majority", meaning: "n. [C] 大多數" },
            { word: "marvel", meaning: "vi. vt. 感到驚奇\nn. [C] 奇蹟；令人驚奇的人或事物" },
            { word: "marvelous", meaning: "adj. 極好的；非凡的" },
            { word: "meanwhile", meaning: "adv. 在此同時；期間" },
            { word: "measurable", meaning: "adj. 可測量的" },
            { word: "measure", meaning: "n. [C] 措施；方法\nvt. 測量" },
            { word: "measurement", meaning: "n. [C, U] 測量" },
            { word: "moral", meaning: "adj. 合乎道德的" },
            { word: "morality", meaning: "n. [U] 道德" },
            { word: "narrow", meaning: "adj. 狹窄的；窄小的\nvt. vi. 使窄小；變窄" },
            { word: "negative", meaning: "adj. 壞的；有害的" },
            { word: "nursery", meaning: "n. [C] 幼兒園" },
            { word: "option", meaning: "n. [C] 選擇；選擇權" },
            { word: "optional", meaning: "adj. 可選擇的" },
            { word: "overflow", meaning: "vt. vi. 溢出；氾濫\nn. [U] 溢出；容納不下的人" },
            { word: "paralyze", meaning: "vt. 使陷入癱瘓；使不能正常運作\nvt. 使癱瘓；使麻痺" },
            { word: "partial", meaning: "adj. 偏愛⋯⋯的；喜歡⋯⋯的\nadj. 不完全的；部分的" },
            { word: "rent", meaning: "vt. vi. 出租；租金為⋯⋯\nn. [C, U] 租金" },
            { word: "rental", meaning: "n. [C, U] 出租；租賃" },
            { word: "revolution", meaning: "n. [C, U] 革命\nn. [C] 巨變；重大變革" },
            { word: "revolutionary", meaning: "adj. 革命性的；巨大轉變的\nadj. 革命的" },
            { word: "salary", meaning: "n. [C] 薪水" },
            { word: "sensible", meaning: "adj. 明智的；合理的" },
            { word: "sin", meaning: "n. [C] 罪；罪過" },
            { word: "strict", meaning: "adj. 嚴格的" },
            { word: "strive", meaning: "vi. 努力；奮鬥" },
            { word: "sway", meaning: "vt. 說服；使相信；使動搖\nvi. 搖擺；搖動" },
            { word: "tenant", meaning: "n. [C] 房客" },
            { word: "thorough", meaning: "adj. 全面的；徹底的" },
            { word: "thoughtful", meaning: "adj. 體貼的；關心的" },
            { word: "thoughtless", meaning: "adj. 粗心的；欠考慮的" },
            { word: "tough", meaning: "adj. 嚴厲的；強硬的\nadj. 艱難的；困苦的" },
            { word: "verdict", meaning: "n. [C] 判決" },
            { word: "version", meaning: "n. [C] （從不同角度的）說法，描述\nn. [C] 版本" },
            { word: "victor", meaning: "n. [C] 勝利者" },
            { word: "victory", meaning: "n. [C, U] 勝利；成功" },
            { word: "widow", meaning: "n. [C] 寡婦；遺孀" },
            { word: "widowed", meaning: "adj. 守寡的；喪偶的" },
            { word: "widower", meaning: "n. [C] 鰥夫" },
            // 片語與慣用語
            { word: "(take…) day off", meaning: "休假" },
            { word: "look after", meaning: "照顧" },
            { word: "take to the streets", meaning: "走上街頭；上街示威遊行" },
            { word: "come to a standstill", meaning: "停頓" },
            { word: "take care of", meaning: "(1)照顧；(2)負責；處理" },
            { word: "take (…) steps", meaning: "採取措施" },
            { word: "in this regard", meaning: "在這方面" },
            { word: "in question", meaning: "討論中的⋯⋯" },
            { word: "to such an extent that…", meaning: "到如此程度，以致於⋯⋯" },
            { word: "be fed up (with)", meaning: "厭煩；不愉快" },
            { word: "throw up", meaning: "嘔吐" },
            { word: "take down", meaning: "拿下；拆掉" },
            { word: "stand up for", meaning: "支持；維護" },
            { word: "hold one’s head up", meaning: "昂首挺胸；抬頭起來" },
            { word: "come over", meaning: "過來" },
            { word: "scare…off", meaning: "把⋯⋯嚇跑" },
            { word: "have high hopes", meaning: "對⋯⋯有信心" }
        ];

        // 核心學習狀態變數
        let activeVocabulary = [...vocabularyDatabase]; // 目前符合篩選的複習名單
        let currentIndex = 0;
        let isFlipped = false;
        let autoplayTimer = null;
        let isAutoplayActive = false;

        // 使用 LocalStorage 持久化使用者收藏與已學狀態
        let userStarredWords = JSON.parse(localStorage.getItem('vocab_starred')) || [];
        let userLearnedWords = JSON.parse(localStorage.getItem('vocab_learned')) || [];

        // 語音發音設定
        let speechRate = parseFloat(localStorage.getItem('vocab_speech_rate')) || 0.9;
        let speechLang = localStorage.getItem('vocab_speech_lang') || 'en-US';

        // 測驗專用狀態變數
        let quizQuestions = [];
        let quizCurrentIndex = 0;
        let quizScore = 0;
        let selectedOptionText = null;

        // 初始化
        window.onload = function() {
            // 還原設定選項 UI
            document.getElementById('setting-rate').value = speechRate;
            document.getElementById('val-rate').innerText = `${speechRate}x`;
            document.getElementById('setting-voice').value = speechLang;

            applyFilters(); // 套用篩選器並刷新卡片
            renderWordList(); // 預渲染單字表
        }

        // ==========================================
        //         1. 閃卡核心操作邏輯
        // ==========================================
        function updateUI() {
            // 切換新卡片時預設恢復為正面(英文)
            isFlipped = false;
            document.getElementById('card').classList.remove('flipped');
            
            if (activeVocabulary.length === 0) {
                // 如果目前篩選範圍內沒有單字
                document.getElementById('wordDisplay').innerText = "📭 篩選清單為空";
                document.getElementById('meaningDisplay').innerText = "請嘗試更改側邊欄的篩選條件，或將單字加上星號/取消已學會標記。";
                document.getElementById('progressIndicator').innerText = "0 / 0";
                document.getElementById('progressBar').style.width = "0%";
                updateStatusBadges(null);
                return;
            }

            const currentItem = activeVocabulary[currentIndex];
            document.getElementById('wordDisplay').innerText = currentItem.word;
            document.getElementById('meaningDisplay').innerText = currentItem.meaning;
            
            // 更新進度與進度條
            document.getElementById('progressIndicator').innerText = `${currentIndex + 1} / ${activeVocabulary.length}`;
            const progressPercent = ((currentIndex + 1) / activeVocabulary.length) * 100;
            document.getElementById('progressBar').style.width = `${progressPercent}%`;

            updateStatusBadges(currentItem);
        }

        function updateStatusBadges(item) {
            const starBtn = document.getElementById('starIcon');
            const starTxt = document.getElementById('starTxt');
            const learnedBadge = document.getElementById('learnedBadge');
            const learnedTxt = document.getElementById('learnedTxt');

            if (!item) {
                starBtn.setAttribute('fill', 'none');
                starBtn.classList.remove('text-amber-400');
                starTxt.innerText = "標記星號";
                learnedBadge.className = "w-3.5 h-3.5 rounded-full border-2 border-slate-500 inline-block";
                learnedTxt.innerText = "標記為已學會";
                return;
            }

            // 更新星號 UI
            if (userStarredWords.includes(item.word)) {
                starBtn.setAttribute('fill', 'currentColor');
                starBtn.classList.add('text-amber-400');
                starTxt.innerText = "已收藏星號";
            } else {
                starBtn.setAttribute('fill', 'none');
                starBtn.classList.remove('text-amber-400');
                starTxt.innerText = "標記星號";
            }

            // 更新已學會 UI
            if (userLearnedWords.includes(item.word)) {
                learnedBadge.className = "w-3.5 h-3.5 rounded-full bg-emerald-500 border border-emerald-500 inline-block";
                learnedTxt.innerText = "已學會！";
            } else {
                learnedBadge.className = "w-3.5 h-3.5 rounded-full border-2 border-slate-500 inline-block";
                learnedTxt.innerText = "標記為已學會";
            }
        }

        function toggleFlip() {
            isFlipped = !isFlipped;
            if (isFlipped) {
                document.getElementById('card').classList.add('flipped');
            } else {
                document.getElementById('card').classList.remove('flipped');
            }
        }

        function nextCard() {
            if (activeVocabulary.length === 0) return;
            currentIndex = (currentIndex + 1) % activeVocabulary.length;
            updateUI();
        }

        function prevCard() {
            if (activeVocabulary.length === 0) return;
            currentIndex = (currentIndex - 1 + activeVocabulary.length) % activeVocabulary.length;
            updateUI();
        }

        // 發音功能 (Web Speech API)
        function speakWord(event, targetWord = null) {
            if (event) event.stopPropagation(); // 阻止卡片翻轉
            
            const wordToSpeak = targetWord || (activeVocabulary.length > 0 ? activeVocabulary[currentIndex].word : null);
            if (!wordToSpeak) return;

            // 先暫停其他發音避免重疊
            window.speechSynthesis.cancel();

            // 乾淨讀出單字（若包含括號/撇號等輔助記號，做基本過濾）
            let cleanWord = wordToSpeak.replace(/\(take…\)/i, 'take').replace(/…/g, '');

            const utterance = new SpeechSynthesisUtterance(cleanWord);
            utterance.lang = speechLang;
            utterance.rate = speechRate;
            window.speechSynthesis.speak(utterance);
        }

        function updateSpeechSettings() {
            speechRate = parseFloat(document.getElementById('setting-rate').value);
            document.getElementById('val-rate').innerText = `${speechRate}x`;
            localStorage.setItem('vocab_speech_rate', speechRate);

            speechLang = document.getElementById('setting-voice').value;
            localStorage.setItem('vocab_speech_lang', speechLang);
        }

        // 自動播放模式 (Autoplay)
        function toggleAutoplay() {
            isAutoplayActive = !isAutoplayActive;
            const btn = document.getElementById('btn-autoplay');
            const ind = document.getElementById('autoplay-indicator');
            const icon = document.getElementById('autoplay-icon');
            const txt = document.getElementById('autoplay-txt');

            if (isAutoplayActive) {
                btn.className = "p-2.5 rounded-xl bg-emerald-950/80 hover:bg-emerald-900 border border-emerald-700 text-emerald-300 transition-all active:scale-90 flex items-center gap-2";
                ind.classList.remove('hidden');
                icon.innerText = "⏸️";
                txt.innerText = "暫停播放";
                runAutoplayLoop();
            } else {
                btn.className = "p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700/60 text-slate-300 transition-all active:scale-90 flex items-center gap-2";
                ind.classList.add('hidden');
                icon.innerText = "▶️";
                txt.innerText = "自動播放";
                clearTimeout(autoplayTimer);
            }
        }

        function runAutoplayLoop() {
            if (!isAutoplayActive || activeVocabulary.length === 0) return;
            
            // 步驟 1: 發音
            speakWord(null);
            
            // 步驟 2: 1.5 秒後翻面
            autoplayTimer = setTimeout(() => {
                if (!isFlipped) toggleFlip();
                
                // 步驟 3: 再 2.5 秒後跳到下一張
                autoplayTimer = setTimeout(() => {
                    nextCard();
                    // 迴圈繼續
                    runAutoplayLoop();
                }, 2500);

            }, 1500);
        }

        // ==========================================
        //         2. 標記與篩選邏輯
        // ==========================================
        function toggleCurrentStar() {
            if (activeVocabulary.length === 0) return;
            const currentItem = activeVocabulary[currentIndex];
            const idx = userStarredWords.indexOf(currentItem.word);

            if (idx > -1) {
                userStarredWords.splice(idx, 1);
            } else {
                userStarredWords.push(currentItem.word);
            }

            localStorage.setItem('vocab_starred', JSON.stringify(userStarredWords));
            updateStatusBadges(currentItem);
            updateGlobalListStats();
        }

        function toggleCurrentLearned() {
            if (activeVocabulary.length === 0) return;
            const currentItem = activeVocabulary[currentIndex];
            const idx = userLearnedWords.indexOf(currentItem.word);

            if (idx > -1) {
                userLearnedWords.splice(idx, 1);
            } else {
                userLearnedWords.push(currentItem.word);
            }

            localStorage.setItem('vocab_learned', JSON.stringify(userLearnedWords));
            updateStatusBadges(currentItem);
            updateGlobalListStats();
        }

        function applyFilters() {
            const filterType = document.getElementById('filterSelect').value;
            
            if (filterType === 'all') {
                activeVocabulary = [...vocabularyDatabase];
            } else if (filterType === 'starred') {
                activeVocabulary = vocabularyDatabase.filter(item => userStarredWords.includes(item.word));
            } else if (filterType === 'unlearned') {
                activeVocabulary = vocabularyDatabase.filter(item => !userLearnedWords.includes(item.word));
            } else if (filterType === 'learned') {
                activeVocabulary = vocabularyDatabase.filter(item => userLearnedWords.includes(item.word));
            }

            currentIndex = 0;
            document.getElementById('stats-current-count').innerText = activeVocabulary.length;
            updateUI();
        }

        function shuffleCurrentList() {
            if (activeVocabulary.length === 0) return;
            // Fisher-Yates Shuffle
            for (let i = activeVocabulary.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [activeVocabulary[i], activeVocabulary[j]] = [activeVocabulary[j], activeVocabulary[i]];
            }
            currentIndex = 0;
            updateUI();
        }

        function resetLearningProgress() {
            if (confirm("確定要清除所有單字的『星號』與『已學會』紀錄嗎？此動作無法復原。")) {
                userStarredWords = [];
                userLearnedWords = [];
                localStorage.removeItem('vocab_starred');
                localStorage.removeItem('vocab_learned');
                applyFilters();
                updateGlobalListStats();
                renderWordList();
            }
        }

        function updateGlobalListStats() {
            document.getElementById('list-stats-starred').innerText = userStarredWords.length;
            document.getElementById('list-stats-learned').innerText = userLearnedWords.length;
        }

        // ==========================================
        //         3. 導覽模式切換
        // ==========================================
        function switchMode(mode) {
            // 先關閉自動播放
            if (isAutoplayActive) toggleAutoplay();

            const pFlashcard = document.getElementById('panel-flashcard');
            const pList = document.getElementById('panel-list');
            const pQuiz = document.getElementById('panel-quiz');

            const bFlashcard = document.getElementById('btn-mode-flashcard');
            const bList = document.getElementById('btn-mode-list');
            const bQuiz = document.getElementById('btn-mode-quiz');

            // 隱藏所有
            pFlashcard.classList.add('hidden');
            pList.classList.add('hidden');
            pQuiz.classList.add('hidden');

            bFlashcard.className = "px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 text-slate-400 hover:text-slate-200";
            bList.className = "px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 text-slate-400 hover:text-slate-200";
            bQuiz.className = "px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 text-slate-400 hover:text-slate-200";

            if (mode === 'flashcard') {
                pFlashcard.classList.remove('hidden');
                bFlashcard.className = "px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 bg-indigo-600 text-white shadow";
                applyFilters();
            } else if (mode === 'list') {
                pList.classList.remove('hidden');
                bList.className = "px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 bg-indigo-600 text-white shadow";
                updateGlobalListStats();
                renderWordList();
            } else if (mode === 'quiz') {
                pQuiz.classList.remove('hidden');
                bQuiz.className = "px-4 py-1.5 rounded-lg text-sm font-semibold transition-all duration-200 bg-indigo-600 text-white shadow";
                backToQuizIntro();
            }
        }

        // ==========================================
        //         4. 單字清單渲染與操作
        // ==========================================
        function renderWordList() {
            const tableBody = document.getElementById('vocabulary-table-body');
            const searchQuery = document.getElementById('listSearch').value.toLowerCase().trim();
            tableBody.innerHTML = "";

            const filteredWords = vocabularyDatabase.filter(item => {
                return item.word.toLowerCase().includes(searchQuery) || item.meaning.toLowerCase().includes(searchQuery);
            });

            if (filteredWords.length === 0) {
                document.getElementById('list-empty-msg').classList.remove('hidden');
                return;
            } else {
                document.getElementById('list-empty-msg').classList.add('hidden');
            }

            filteredWords.forEach(item => {
                const isStarred = userStarredWords.includes(item.word);
                const isLearned = userLearnedWords.includes(item.word);

                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-800/40 transition-colors border-b border-slate-800/60";
                
                tr.innerHTML = `
                    <td class="py-3 px-6 text-center">
                        <button onclick="speakWord(event, '${item.word}')" class="p-2 bg-slate-800 hover:bg-slate-700 text-indigo-400 rounded-lg inline-block" title="發音">
                            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M11 5L6 9H2v6h4l5 4V5z"></path><path d="M19.07 4.93a10 10 0 0 1 0 14.14M15.54 8.46a5 5 0 0 1 0 7.07"></path></svg>
                        </button>
                    </td>
                    <td class="py-3 px-6 font-bold text-white tracking-wide">${item.word}</td>
                    <td class="py-3 px-6 text-slate-300 leading-relaxed whitespace-pre-line">${item.meaning}</td>
                    <td class="py-3 px-6 text-center">
                        <div class="flex items-center justify-center gap-2">
                            <button onclick="toggleListStar('${item.word}')" class="p-1 text-slate-500 hover:text-amber-400" title="收藏">
                                <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="${isStarred ? 'currentColor' : 'none'}" stroke="currentColor" stroke-width="2" class="${isStarred ? 'text-amber-400' : ''}"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>
                            </button>
                            <button onclick="toggleListLearned('${item.word}')" class="p-1" title="已學會">
                                <span class="w-4 h-4 rounded-full border-2 block ${isLearned ? 'bg-emerald-500 border-emerald-500' : 'border-slate-600'}"></span>
                            </button>
                        </div>
                    </td>
                `;
                tableBody.appendChild(tr);
            });
            updateGlobalListStats();
        }

        function toggleListStar(word) {
            const idx = userStarredWords.indexOf(word);
            if (idx > -1) userStarredWords.splice(idx, 1);
            else userStarredWords.push(word);
            localStorage.setItem('vocab_starred', JSON.stringify(userStarredWords));
            renderWordList();
        }

        function toggleListLearned(word) {
            const idx = userLearnedWords.indexOf(word);
            if (idx > -1) userLearnedWords.splice(idx, 1);
            else userLearnedWords.push(word);
            localStorage.setItem('vocab_learned', JSON.stringify(userLearnedWords));
            renderWordList();
        }

        // ==========================================
        //         5. 測驗模式邏輯
        // ==========================================
        function startQuiz() {
            const scope = document.getElementById('quizScope').value;
            let pool = [...vocabularyDatabase];

            if (scope === 'starred') {
                pool = vocabularyDatabase.filter(item => userStarredWords.includes(item.word));
                if (pool.length < 4) {
                    alert("⚠️ 您的星號收藏中單字不足 4 個，無法舉行測驗！請先至閃卡或清單模式中標記一些星號單字。");
                    return;
                }
            }

            // 初始化測驗
            quizQuestions = [];
            quizCurrentIndex = 0;
            quizScore = 0;

            // 隨機抽 10 題
            const shuffledPool = [...pool].sort(() => 0.5 - Math.random());
            const limit = Math.min(10, shuffledPool.length);
            
            for (let i = 0; i < limit; i++) {
                const questionWord = shuffledPool[i];
                // 產生四個選項（其中一個是正確答案，另外三個是隨機干擾選項）
                const options = [questionWord.meaning];
                const distractors = pool.filter(item => item.word !== questionWord.word);
                const shuffledDistractors = distractors.sort(() => 0.5 - Math.random());
                
                for (let d = 0; d < Math.min(3, shuffledDistractors.length); d++) {
                    options.push(shuffledDistractors[d].meaning);
                }

                // 選項亂序
                options.sort(() => 0.5 - Math.random());

                quizQuestions.push({
                    word: questionWord.word,
                    answer: questionWord.meaning,
                    options: options
                });
            }

            document.getElementById('quiz-intro-card').classList.add('hidden');
            document.getElementById('quiz-play-card').classList.remove('hidden');
            document.getElementById('quiz-result-card').classList.add('hidden');

            showQuizQuestion();
        }

        function showQuizQuestion() {
            const q = quizQuestions[quizCurrentIndex];
            document.getElementById('quiz-current-idx').innerText = quizCurrentIndex + 1;
            document.getElementById('quiz-score').innerText = quizScore;
            document.getElementById('quiz-question-word').innerText = q.word;

            const optContainer = document.getElementById('quiz-options-container');
            optContainer.innerHTML = "";

            // 發音當前考題單字
            speakQuizWord();

            q.options.forEach((opt, idx) => {
                const btn = document.createElement('button');
                btn.className = "w-full text-left p-4 rounded-xl bg-slate-900 border border-slate-700 hover:border-slate-500 hover:bg-slate-800 text-slate-200 transition-all font-medium flex justify-between items-center text-sm md:text-base";
                btn.onclick = () => checkQuizAnswer(btn, opt, q.answer);
                btn.innerHTML = `
                    <span>${opt}</span>
                    <span class="text-slate-600 text-xs font-semibold select-none">選項 ${idx + 1}</span>
                `;
                optContainer.appendChild(btn);
            });

            document.getElementById('btn-quiz-next').disabled = true;
        }

        function speakQuizWord() {
            if (quizQuestions.length > 0) {
                const word = quizQuestions[quizCurrentIndex].word;
                speakWord(null, word);
            }
        }

        function checkQuizAnswer(selectedBtn, chosenOpt, correctOpt) {
            const optButtons = document.querySelectorAll('#quiz-options-container button');
            
            // 停用所有按鈕點擊，防止重複回答
            optButtons.forEach(btn => btn.onclick = null);

            if (chosenOpt === correctOpt) {
                selectedBtn.className = "w-full text-left p-4 rounded-xl bg-emerald-950/80 border-2 border-emerald-500 text-emerald-200 transition-all font-semibold flex justify-between items-center text-sm md:text-base";
                selectedBtn.innerHTML += `<span class="text-emerald-400 font-extrabold text-base">✓ 答對</span>`;
                quizScore += 10;
                document.getElementById('quiz-score').innerText = quizScore;
            } else {
                selectedBtn.className = "w-full text-left p-4 rounded-xl bg-red-950/80 border-2 border-red-500 text-red-200 transition-all font-semibold flex justify-between items-center text-sm md:text-base";
                selectedBtn.innerHTML += `<span class="text-red-400 font-extrabold text-base">✗ 答錯</span>`;
                
                // 將正確答案用綠色框特別標出
                optButtons.forEach(btn => {
                    const textSpan = btn.querySelector('span');
                    if (textSpan && textSpan.innerText.trim() === correctOpt.trim()) {
                        btn.className = "w-full text-left p-4 rounded-xl bg-slate-900 border-2 border-emerald-500/60 text-emerald-300 transition-all font-medium flex justify-between items-center text-sm md:text-base";
                    }
                });
            }

            document.getElementById('btn-quiz-next').disabled = false;
        }

        function nextQuizQuestion() {
            quizCurrentIndex++;
            if (quizCurrentIndex < quizQuestions.length) {
                showQuizQuestion();
            } else {
                showQuizResult();
            }
        }

        function showQuizResult() {
            document.getElementById('quiz-play-card').classList.add('hidden');
            document.getElementById('quiz-result-card').classList.remove('hidden');

            const percent = Math.round((quizScore / (quizQuestions.length * 10) * 100)) || 0;
            document.getElementById('quiz-res-percent').innerText = `${percent}%`;
            document.getElementById('quiz-res-score').innerText = quizScore;
        }

        function backToQuizIntro() {
            document.getElementById('quiz-intro-card').classList.remove('hidden');
            document.getElementById('quiz-play-card').classList.add('hidden');
            document.getElementById('quiz-result-card').classList.add('hidden');
        }

        // ==========================================
        //         6. 全域鍵盤快捷鍵支援
        // ==========================================
        document.addEventListener('keydown', (e) => {
            // 只有當位於閃卡模式時，才作用快捷鍵
            const isFlashcardActive = !document.getElementById('panel-flashcard').classList.contains('hidden');
            if (!isFlashcardActive) return;

            if (e.key === ' ' || e.key === 'ArrowUp' || e.key === 'ArrowDown') {
                e.preventDefault();
                toggleFlip();
            } else if (e.key === 'ArrowRight') {
                nextCard();
            } else if (e.key === 'ArrowLeft') {
                prevCard();
            } else if (e.key === 'v' || e.key === 'V') {
                speakWord();
            }
        });
    </script>
</body>
</html>
