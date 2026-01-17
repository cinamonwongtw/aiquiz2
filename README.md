<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI 成熟度評估 | AI Maturity Assessment</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Alpine.js -->
    <script src="https://cdn.jsdelivr.net/npm/alpinejs@3.13.3/dist/cdn.min.js"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        dark: {
                            900: '#0f172a',
                            800: '#1e293b',
                            700: '#334155',
                        },
                        brand: {
                            400: '#818cf8',
                            500: '#6366f1',
                            600: '#4f46e5',
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'Microsoft JhengHei', 'sans-serif'],
                    },
                    animation: {
                        'pulse-slow': 'pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                    }
                }
            }
        }
    </script>
    <style>
        [x-cloak] { display: none !important; }
        .fade-enter-active, .fade-leave-active { transition: opacity 0.5s; }
        .fade-enter, .fade-leave-to { opacity: 0; }
        body { background-color: #0f172a; color: #e2e8f0; }
        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #1e293b; }
        ::-webkit-scrollbar-thumb { background: #475569; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #64748b; }
    </style>
</head>
<body class="antialiased font-sans min-h-screen flex flex-col items-center justify-center p-4 selection:bg-brand-500 selection:text-white"
      x-data="assessmentApp()">

    <!-- Header / Progress -->
    <header class="fixed top-0 left-0 w-full bg-dark-900/90 backdrop-blur-md border-b border-dark-700 z-50 h-16 flex items-center justify-between px-6">
        <div class="flex items-center gap-2">
            <div class="w-8 h-8 rounded-lg bg-gradient-to-br from-brand-400 to-purple-600 flex items-center justify-center text-white font-bold text-lg shadow-lg shadow-brand-500/20">AI</div>
            <h1 class="text-lg font-bold tracking-wide text-white hidden sm:block">AI Maturity Assessment</h1>
        </div>
        
        <!-- Progress Bar (Only show during quiz) -->
        <div class="flex-1 max-w-md mx-4" x-show="step === 'quiz'" x-transition>
            <div class="w-full bg-dark-700 rounded-full h-2.5 overflow-hidden">
                <div class="bg-gradient-to-r from-brand-400 to-purple-500 h-2.5 rounded-full transition-all duration-500 ease-out relative" :style="`width: ${progress}%`">
                    <div class="absolute inset-0 bg-white/20 animate-pulse-slow"></div>
                </div>
            </div>
            <div class="text-xs text-slate-400 text-right mt-1 font-mono">
                <span x-text="currentQuestionIndex + 1"></span> / <span x-text="questions.length"></span>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="w-full max-w-2xl mt-20 mb-10 relative z-10">
        
        <!-- STEP 1: Intro Card -->
        <section x-show="step === 'intro'" x-transition.opacity.duration.500ms class="bg-dark-800/80 backdrop-blur-sm rounded-2xl p-8 border border-dark-700 shadow-2xl text-center relative overflow-hidden">
             <!-- Decorative bg elements -->
             <div class="absolute -top-24 -left-24 w-48 h-48 bg-brand-500/10 rounded-full blur-3xl"></div>
             <div class="absolute -bottom-24 -right-24 w-48 h-48 bg-purple-500/10 rounded-full blur-3xl"></div>

            <div class="mb-6 inline-block p-4 rounded-full bg-dark-700/50 ring-1 ring-dark-600 shadow-inner relative z-10">
                <svg class="w-12 h-12 text-brand-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"></path></svg>
            </div>
            <h2 class="text-3xl font-bold text-white mb-4 relative z-10">5 級 AI 成熟度評估</h2>
            <p class="text-slate-300 mb-6 leading-relaxed relative z-10">
                您的 AI 使用方式是處於「玩具階段」還是「策略整合」？<br>
                這份測驗基於 <strong>5-Level AI Maturity Framework</strong>，透過 10 個行為特徵問題，精準分析您的 AI 素養等級，並提供客製化的升級指南。
            </p>
            <div class="space-y-2 text-sm text-slate-400 text-left bg-dark-900/50 p-5 rounded-xl border border-dark-700 mb-8 relative z-10">
                <div class="flex items-center gap-2">
                    <span class="text-brand-400">⏱️</span> <span>預計時間：2 分鐘</span>
                </div>
                <div class="flex items-center gap-2">
                    <span class="text-brand-400">🎯</span> <span>分析維度：心態認知、提示工程、工作流整合</span>
                </div>
                <div class="flex items-center gap-2">
                    <span class="text-brand-400">📈</span> <span>輸出報告：成熟度等級 + 雷達圖 + 學習路徑</span>
                </div>
            </div>
            <button @click="startQuiz()" class="w-full sm:w-auto px-8 py-3.5 bg-gradient-to-r from-brand-600 to-brand-500 hover:from-brand-500 hover:to-brand-400 text-white rounded-xl font-bold shadow-lg shadow-brand-500/30 transition-all transform hover:scale-[1.02] active:scale-[0.98] relative z-10 tracking-wide">
                開始評估
            </button>
        </section>

        <!-- STEP 2: Quiz Card -->
        <section x-show="step === 'quiz'" x-cloak class="bg-dark-800 rounded-2xl p-6 sm:p-8 border border-dark-700 shadow-2xl min-h-[400px] flex flex-col justify-between transition-all duration-300">
            
            <!-- Question -->
            <div class="animate-fadeIn">
                <div class="flex items-center justify-between mb-4">
                    <span class="px-3 py-1 rounded-full bg-brand-500/10 text-brand-400 text-xs font-bold tracking-wider uppercase border border-brand-500/20" x-text="currentQuestion.category"></span>
                </div>
                
                <h3 class="text-xl sm:text-2xl font-bold text-white mb-8 leading-snug" x-text="currentQuestion.text"></h3>

                <!-- Options -->
                <div class="space-y-3">
                    <template x-for="(option, index) in currentQuestion.options" :key="index">
                        <button @click="selectOption(option.score)"
                                class="w-full text-left p-4 rounded-xl border border-dark-700 bg-dark-900/50 hover:bg-dark-700 hover:border-brand-500/50 transition-all duration-200 group flex items-start active:bg-brand-900/20">
                            <div class="flex-shrink-0 mt-0.5 w-6 h-6 rounded-full border-2 border-slate-600 group-hover:border-brand-400 mr-4 flex items-center justify-center transition-colors">
                                <div class="w-2.5 h-2.5 rounded-full bg-brand-400 opacity-0 group-hover:opacity-100 transition-opacity"></div>
                            </div>
                            <span class="text-slate-300 group-hover:text-white text-base leading-relaxed" x-text="option.text"></span>
                        </button>
                    </template>
                </div>
            </div>
        </section>

        <!-- STEP 3: Result Card -->
        <section x-show="step === 'result'" x-cloak class="space-y-8 animate-fadeIn">
            
            <!-- Main Result -->
            <div class="bg-dark-800 rounded-2xl p-8 border border-dark-700 shadow-2xl text-center relative overflow-hidden">
                <!-- Background Glow -->
                <div class="absolute top-0 left-1/2 -translate-x-1/2 w-80 h-80 bg-brand-500/10 blur-[100px] rounded-full pointer-events-none"></div>
                
                <p class="text-brand-400 font-bold tracking-[0.2em] uppercase mb-2 text-sm">Assessment Complete</p>
                <h2 class="text-4xl sm:text-5xl font-black text-transparent bg-clip-text bg-gradient-to-br from-white to-slate-400 mb-2">
                    <span x-text="result.title_en"></span>
                </h2>
                <div class="inline-flex items-center justify-center px-4 py-1 rounded-full bg-dark-700/50 border border-dark-600 mb-6">
                    <h3 class="text-xl text-brand-300 font-bold" x-text="result.title_zh"></h3>
                </div>
                
                <p class="text-slate-300 mb-8 max-w-lg mx-auto leading-relaxed text-base" x-text="result.description"></p>

                <!-- Radar Chart Container -->
                <div class="relative w-full max-w-[320px] mx-auto h-[320px] mb-8 bg-dark-900/50 rounded-full p-4 border border-dark-700/50">
                    <canvas id="radarChart"></canvas>
                </div>

                <div class="grid grid-cols-2 gap-4 text-left">
                    <div class="bg-dark-900/60 p-4 rounded-xl border border-dark-700/80 backdrop-blur-sm">
                        <p class="text-xs text-slate-500 uppercase font-bold tracking-wider mb-1">當前狀態</p>
                        <p class="text-white font-semibold text-lg" x-text="result.status"></p>
                    </div>
                    <div class="bg-dark-900/60 p-4 rounded-xl border border-dark-700/80 backdrop-blur-sm">
                        <p class="text-xs text-slate-500 uppercase font-bold tracking-wider mb-1">核心特徵</p>
                        <p class="text-white font-semibold text-lg" x-text="result.trait"></p>
                    </div>
                </div>
            </div>

            <!-- Next Steps / Action Plan -->
            <div class="bg-gradient-to-br from-dark-800 to-indigo-950/30 rounded-2xl p-6 sm:p-8 border border-brand-500/20 shadow-xl relative overflow-hidden">
                <div class="absolute top-0 right-0 w-32 h-32 bg-brand-500/5 rounded-full blur-2xl pointer-events-none"></div>
                
                <h3 class="text-xl font-bold text-white mb-6 flex items-center border-b border-dark-700 pb-4">
                    <svg class="w-6 h-6 text-brand-400 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path></svg>
                    下一步升級指南 (Next Steps)
                </h3>
                
                <div class="space-y-6">
                    <!-- Key Bottleneck -->
                    <div>
                        <h4 class="text-xs font-bold text-slate-500 uppercase mb-2 tracking-wider">⚠️ 關鍵瓶頸 (Bottleneck)</h4>
                        <p class="text-rose-200 bg-rose-500/10 border border-rose-500/20 p-4 rounded-lg text-sm leading-relaxed" x-text="result.bottleneck"></p>
                    </div>

                    <!-- Core Mission -->
                    <div>
                        <h4 class="text-xs font-bold text-slate-500 uppercase mb-2 tracking-wider">🎯 核心任務 (Mission)</h4>
                        <p class="text-slate-200 bg-dark-900/50 p-4 rounded-lg text-sm border border-dark-700" x-text="result.mission"></p>
                    </div>

                    <!-- Unlock Skills -->
                    <div>
                        <h4 class="text-xs font-bold text-brand-400 uppercase mb-3 tracking-wider">🗝️ 必學解鎖技能 (Unlock Skills)</h4>
                        <ul class="space-y-3">
                            <template x-for="skill in result.skills">
                                <li class="flex items-start bg-dark-900/30 p-3 rounded-lg border border-dark-700/50 hover:border-brand-500/30 transition-colors">
                                    <div class="bg-green-500/20 rounded-full p-1 mr-3 mt-0.5">
                                        <svg class="w-3.5 h-3.5 text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path></svg>
                                    </div>
                                    <span class="text-slate-300 text-sm leading-relaxed" x-html="skill"></span>
                                </li>
                            </template>
                        </ul>
                    </div>
                </div>
            </div>

            <!-- Footer Buttons -->
            <div class="flex flex-col sm:flex-row gap-4 justify-center pb-12">
                <button @click="restart()" class="px-6 py-3 border border-dark-600 text-slate-400 hover:text-white hover:border-brand-500 hover:bg-dark-700 rounded-xl transition font-medium">重新評估</button>
                <button @click="shareResult()" class="px-6 py-3 bg-brand-600 hover:bg-brand-500 text-white rounded-xl shadow-lg shadow-brand-600/20 font-bold transition flex items-center justify-center gap-2 transform active:scale-95">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.684 13.342C8.886 12.938 9 12.482 9 12c0-.482-.114-.938-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.632 3.316m-6.632-6l6.632-3.316m0 0a3 3 0 105.367-2.684 3 3 0 00-5.367 2.684zm0 9.316a3 3 0 105.368 2.684 3 3 0 00-5.368-2.684z"></path></svg>
                    複製結果摘要
                </button>
            </div>
            
            <!-- Toast Notification -->
            <div x-show="showToast" x-transition.opacity.duration.300ms class="fixed bottom-6 left-1/2 transform -translate-x-1/2 bg-white text-dark-900 px-6 py-3 rounded-full shadow-2xl font-semibold z-50 flex items-center gap-2">
                <svg class="w-5 h-5 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path></svg>
                已複製到剪貼簿！
            </div>

        </section>
    </main>

    <script>
        function assessmentApp() {
            return {
                step: 'intro',
                currentQuestionIndex: 0,
                score: 0,
                answers: [], 
                chartInstance: null,
                showToast: false,
                
                // Categories: Mindset (心態), Prompting (技巧), Workflow (整合)
                // Score logic: 1 (Explorer) -> 5 (Builder)
                questions: [
                    {
                        category: "心態認知 (Mindset)",
                        text: "當你需要使用 AI 時，你的第一個直覺想法通常是什麼？",
                        options: [
                            { text: "來聊聊天，看看它會說什麼好笑的，或問一些隨機問題。", score: 1, type: 'mindset' },
                            { text: "我有個具體任務（如寫信、翻譯），希望能幫我省點時間。", score: 2, type: 'mindset' },
                            { text: "這件事我能做，但我希望 AI 給我更好的點子或優化我的初稿。", score: 3, type: 'mindset' },
                            { text: "這是複雜專案的一部分，我需要 AI 處理其中的邏輯分析環節。", score: 4, type: 'mindset' },
                            { text: "我正在思考如何讓這件事完全自動化，讓 AI 代理人去處理。", score: 5, type: 'mindset' }
                        ]
                    },
                    {
                        category: "使用頻率與觸發 (Workflow)",
                        text: "你通常在什麼情況下會打開 ChatGPT 或 Claude？",
                        options: [
                            { text: "偶爾想到才開，沒有固定規律。", score: 1, type: 'workflow' },
                            { text: "遇到繁瑣文書工作（如會議記錄、長文摘要）時。", score: 2, type: 'workflow' },
                            { text: "需要腦力激盪、撰寫專業文案或尋求決策建議時。", score: 3, type: 'workflow' },
                            { text: "在處理多步驟工作流（大綱→草稿→審閱）的過程中持續使用。", score: 4, type: 'workflow' },
                            { text: "它幾乎一直開著，或者是透過 API/外掛自動在背景運行。", score: 5, type: 'workflow' }
                        ]
                    },
                    {
                        category: "提示工程 (Prompting)",
                        text: "你的 Prompt（指令）通常長什麼樣子？",
                        options: [
                            { text: "很短，像搜尋關鍵字。例如：「寫一首詩」或「日本旅遊」。", score: 1, type: 'prompting' },
                            { text: "指令式，有動詞。例如：「幫我總結這篇文章」或「翻譯這段」。", score: 2, type: 'prompting' },
                            { text: "有結構，包含角色設定、背景和限制。例如：「你是一位行銷專家...」。", score: 3, type: 'prompting' },
                            { text: "包含思維鏈 (Let's think step by step) 或提供範例 (Few-shot) 讓它模仿。", score: 4, type: 'prompting' },
                            { text: "模組化的 System Prompt，包含變數與條件判斷邏輯。", score: 5, type: 'prompting' }
                        ]
                    },
                    {
                        category: "錯誤處理 (Mindset)",
                        text: "當 AI 開始「一本正經胡說八道」(幻覺) 時，你會怎麼做？",
                        options: [
                            { text: "覺得它很笨或不可靠，關掉不用了。", score: 1, type: 'mindset' },
                            { text: "重新產生一次 (Regenerate)，看看運氣會不會好一點。", score: 2, type: 'prompting' },
                            { text: "指出它的錯誤，並修正我的指令讓它重寫。", score: 3, type: 'prompting' },
                            { text: "檢查我的 Prompt 邏輯是否不夠嚴謹，引導它一步步推理。", score: 4, type: 'prompting' },
                            { text: "調整系統參數或知識庫來源 (RAG)，從根本解決問題。", score: 5, type: 'workflow' }
                        ]
                    },
                    {
                        category: "價值創造 (Workflow)",
                        text: "AI 對你目前工作最大的價值是什麼？",
                        options: [
                            { text: "娛樂或滿足好奇心。", score: 1, type: 'mindset' },
                            { text: "節能 (Energy Saving)：省下做瑣事的時間。", score: 2, type: 'workflow' },
                            { text: "增能 (Enhancing)：提升產出的品質，做得比我自己好。", score: 3, type: 'workflow' },
                            { text: "賦能 (Enabling)：幫我做到我原本不會的事（如寫程式、畫圖）。", score: 4, type: 'workflow' },
                            { text: "重塑 (Reshaping)：徹底改變了我的商業模式或工作流程。", score: 5, type: 'workflow' }
                        ]
                    },
                    {
                        category: "互動深度 (Prompting)",
                        text: "在完成一個任務時，你與 AI 的對話通常幾輪？",
                        options: [
                            { text: "1 輪，問完就看結果，不行就算了。", score: 1, type: 'prompting' },
                            { text: "1-2 輪，主要是單向指令。", score: 2, type: 'prompting' },
                            { text: "多輪來回，我會像主管一樣審核並要求修改。", score: 3, type: 'prompting' },
                            { text: "長對話，我們像夥伴一樣共同拆解複雜問題。", score: 4, type: 'prompting' },
                            { text: "自動化循環，我設定好規則，讓它自我修正。", score: 5, type: 'prompting' }
                        ]
                    },
                    {
                        category: "技能應用 (Workflow)",
                        text: "面對一個陌生的新領域任務，你會怎麼用 AI？",
                        options: [
                            { text: "問它「這是什麼」，當作百科全書。", score: 1, type: 'mindset' },
                            { text: "叫它幫我找資料或整理重點。", score: 2, type: 'workflow' },
                            { text: "讓它扮演該領域專家，給我決策建議或學習路徑。", score: 3, type: 'prompting' },
                            { text: "利用它跨越技能門檻，例如直接請它寫出那個領域的程式碼或分析報告。", score: 4, type: 'workflow' },
                            { text: "建立一個專屬該領域的知識庫或微調模型。", score: 5, type: 'workflow' }
                        ]
                    },
                    {
                        category: "工作流整合 (Workflow)",
                        text: "AI 在你的工作流程中佔據什麼位置？",
                        options: [
                            { text: "外掛。偶爾想到才用。", score: 1, type: 'workflow' },
                            { text: "工具。處理特定節點（如翻譯郵件）。", score: 2, type: 'workflow' },
                            { text: "實習生。我把初步工作交給它，我負責最後把關。", score: 3, type: 'workflow' },
                            { text: "副駕駛。我們全程協作，它負責執行，我負責策略。", score: 4, type: 'workflow' },
                            { text: "引擎。它是驅動整個業務的核心，我負責監控儀表板。", score: 5, type: 'workflow' }
                        ]
                    },
                    {
                        category: "風險意識 (Mindset)",
                        text: "關於 AI 的資料隱私和產出正確性，你的態度是？",
                        options: [
                            { text: "沒想太多，反正它給什麼我就信什麼。", score: 1, type: 'mindset' },
                            { text: "會稍微看一下有沒有明顯錯誤。", score: 2, type: 'mindset' },
                            { text: "我會進行事實查核 (Fact Check)，並小心不輸入敏感個資。", score: 3, type: 'mindset' },
                            { text: "我有建立 Human-in-the-Loop 機制，關鍵節點必由人審查。", score: 4, type: 'workflow' },
                            { text: "我關注 AI 治理與合規，並在系統層級設計防護網。", score: 5, type: 'workflow' }
                        ]
                    },
                    {
                        category: "未來展望 (Mindset)",
                        text: "你如何看待自己未來與 AI 的關係？",
                        options: [
                            { text: "有點擔心被取代，或者是覺得它只是風潮。", score: 1, type: 'mindset' },
                            { text: "希望能幫我少加點班，早點下班。", score: 2, type: 'mindset' },
                            { text: "我要成為更強的專業人士，讓它放大我的能力。", score: 3, type: 'mindset' },
                            { text: "我要成為跨領域的創造者，做以前做不到的事。", score: 4, type: 'mindset' },
                            { text: "我要成為架構師，設計讓 AI 為我工作的系統。", score: 5, type: 'mindset' }
                        ]
                    }
                ],

                // Level Definitions
                levels: {
                    explorer: {
                        title_en: "Level 1: The Explorer",
                        title_zh: "探索者 (Crawl)",
                        status: "概念觀望期",
                        trait: "玩具心態 / 隨機使用",
                        description: "您目前處於「理解術語」階段。AI 對您來說可能更像是一個有趣的玩具或搜尋引擎，而非生產力工具。您可能偶爾使用它，但缺乏明確的目的或技巧，容易因 AI 的幻覺而感到挫折。",
                        bottleneck: "不知道 AI 到底能解決什麼具體問題，或對其準確性缺乏信心。",
                        mission: "從「隨機聊天」轉向「功能性應用」，建立驗證資訊的習慣。",
                        skills: [
                            "<strong>基礎指令工程 (Verbs)</strong>：使用明確動詞（總結、翻譯、改寫）而非關鍵字。",
                            "<strong>識別節能場景</strong>：找出工作中耗時但低風險的任務（如 OCR、逐字稿整理）。",
                            "<strong>建立驗證習慣</strong>：不照單全收，學會查核 AI 提供的資訊。"
                        ]
                    },
                    operator: {
                        title_en: "Level 2: The Operator",
                        title_zh: "操作者 (Walk)",
                        status: "節能階段 (Energy Saving)",
                        trait: "單點任務 / 效率導向",
                        description: "您已進入「節能模式」，懂得利用 AI 處理耗時但低風險的瑣事（如翻譯、摘要）。您具備基本的提問能力，但多為單次指令，較少進行來回優化。心態上偏向「工人思維」，專注於完成眼前任務。",
                        bottleneck: "產出品質不穩定，且僅限於單點任務，缺乏系統性。",
                        mission: "從「單次指令」轉向「結構化溝通」，像主管一樣管理 AI。",
                        skills: [
                            "<strong>角色提示 (Role Prompting)</strong>：賦予 AI 專家身分（如：你是一位資深編輯）。",
                            "<strong>結構化提示</strong>：包含角色、任務、背景、限制四要素。",
                            "<strong>格式控制</strong>：要求 AI 輸出表格、JSON 或特定格式以便直接使用。"
                        ]
                    },
                    instructor: {
                        title_en: "Level 3: The Instructor",
                        title_zh: "指揮者 (Run)",
                        status: "增能階段 (Enhancing)",
                        trait: "主管心態 / 迭代優化",
                        description: "您具備「主管心態」，將 AI 視為能力強但不穩定的「實習生」。您熟練運用「想清楚、說清楚、看清楚」的循環，懂得透過多輪對話與結構化指令來提升專業品質。您不僅是使用者，更是 AI 的教練。",
                        bottleneck: "只能做單點任務優化，尚未將多個 AI 工具串聯成自動化流程。",
                        mission: "從「單點任務」進階到「工作流重塑」，串聯多個工具。",
                        skills: [
                            "<strong>思維鏈 (CoT)</strong>：使用「請一步步思考」減少邏輯錯誤。",
                            "<strong>少樣本提示 (Few-Shot)</strong>：提供高品質範例讓 AI 模仿風格與邏輯。",
                            "<strong>工具組合 (Tool Chaining)</strong>：了解不同模型強項（如 Claude 分析、GPT 創意）並組合使用。"
                        ]
                    },
                    collaborator: {
                        title_en: "Level 4: The Collaborator",
                        title_zh: "協作者 (Fly)",
                        status: "賦能階段 (Enabling)",
                        trait: "跨域創造 / 深度整合",
                        description: "您已將 AI 深度整合至複雜的工作流中，與 AI 形成互補的夥伴關係。您利用 AI 突破了自身專業限制（如文科生寫 Code），並具備「工頭思維」，讓 AI 處理大部分執行工作，自己專注於策略與決策。",
                        bottleneck: "手動複製貼上仍然很累，需要更多自動化與系統化。",
                        mission: "突破自身邊界，利用自動化構建系統，實現規模化。",
                        skills: [
                            "<strong>AI 輔助編碼</strong>：用自然語言指揮 AI 撰寫 Python 腳本或 Excel 複雜公式。",
                            "<strong>工作流自動化</strong>：使用 Zapier/Make 將 AI 接入日常流程（如 Email 自動摘要）。",
                            "<strong>多模態提示</strong>：結合圖文理解，處理更複雜的輸入輸出。"
                        ]
                    },
                    builder: {
                        title_en: "Level 5: The Builder",
                        title_zh: "構建者 (Scale)",
                        status: "重塑階段 (Reshaping)",
                        trait: "建築師思維 / 生態系建構",
                        description: "您處於金字塔頂端，利用 AI 開拓新能力、重塑商業模式。您具備「建築師思維」，可能正在開發專屬的 AI Agent 或建立組織內的 AI 知識庫。您是 AI 應用的創新者，始終掌握方向與決策。",
                        bottleneck: "如何治理 AI 代理人網路，以及確保大規模運作的安全性與倫理。",
                        mission: "設計系統、制定標準，將 AI 內化為核心競爭力。",
                        skills: [
                            "<strong>Agent 設計</strong>：設計能獨立規劃並執行任務的 AI 代理人。",
                            "<strong>HITL/HOTL 架構</strong>：設計人類介入監督的機制，確保自動化系統安全。",
                            "<strong>知識庫與微調 (RAG)</strong>：將私有資料餵給 AI，建立專屬大腦。"
                        ]
                    }
                },

                result: {},
                
                get progress() {
                    return ((this.currentQuestionIndex) / this.questions.length) * 100;
                },

                get currentQuestion() {
                    return this.questions[this.currentQuestionIndex];
                },

                startQuiz() {
                    this.step = 'quiz';
                    this.currentQuestionIndex = 0;
                    this.score = 0;
                    this.answers = [];
                    // Reset scroll
                    window.scrollTo({ top: 0, behavior: 'smooth' });
                },

                selectOption(score) {
                    // Save detailed data for radar chart
                    const currentQ = this.questions[this.currentQuestionIndex];
                    this.answers.push({
                        category: currentQ.options.find(o => o.score === score).type,
                        score: score
                    });

                    this.score += score;

                    if (this.currentQuestionIndex < this.questions.length - 1) {
                        this.currentQuestionIndex++;
                    } else {
                        this.calculateResult();
                    }
                },

                calculateResult() {
                    // Logic: Average score maps to levels
                    const avgScore = this.score / this.questions.length;
                    
                    let levelKey = '';
                    if (avgScore <= 1.8) levelKey = 'explorer';
                    else if (avgScore <= 2.6) levelKey = 'operator';
                    else if (avgScore <= 3.4) levelKey = 'instructor';
                    else if (avgScore <= 4.2) levelKey = 'collaborator';
                    else levelKey = 'builder';
                    
                    this.result = this.levels[levelKey];
                    this.step = 'result';
                    
                    // Wait for DOM update then render chart
                    setTimeout(() => {
                        this.renderChart();
                        window.scrollTo({ top: 0, behavior: 'smooth' });
                    }, 100);
                },

                renderChart() {
                    // Aggregate scores by category
                    const categories = {
                        'mindset': { sum: 0, count: 0, label: '心態認知' },
                        'prompting': { sum: 0, count: 0, label: '提示技巧' },
                        'workflow': { sum: 0, count: 0, label: '工作流整合' }
                    };

                    this.answers.forEach(a => {
                        if (categories[a.category]) {
                            categories[a.category].sum += a.score;
                            categories[a.category].count += 1;
                        }
                    });

                    const dataPoints = [
                        (categories['mindset'].sum / categories['mindset'].count) || 0,
                        (categories['prompting'].sum / categories['prompting'].count) || 0,
                        (categories['workflow'].sum / categories['workflow'].count) || 0
                    ];

                    const ctx = document.getElementById('radarChart');
                    
                    // Destroy previous chart if exists
                    if (this.chartInstance) {
                        this.chartInstance.destroy();
                    }

                    Chart.defaults.color = '#94a3b8';
                    Chart.defaults.borderColor = '#334155';

                    this.chartInstance = new Chart(ctx, {
                        type: 'radar',
                        data: {
                            labels: ['心態認知', '提示技巧', '工作流整合'],
                            datasets: [{
                                label: '您的能力分布',
                                data: dataPoints,
                                backgroundColor: 'rgba(99, 102, 241, 0.2)', // brand-500 with opacity
                                borderColor: '#818cf8', // brand-400
                                borderWidth: 2,
                                pointBackgroundColor: '#fff',
                                pointBorderColor: '#6366f1',
                                pointHoverBackgroundColor: '#fff',
                                pointHoverBorderColor: '#6366f1'
                            }]
                        },
                        options: {
                            scales: {
                                r: {
                                    angleLines: { color: '#334155' },
                                    grid: { color: '#334155' },
                                    pointLabels: {
                                        font: { size: 12, weight: 'bold' },
                                        color: '#cbd5e1'
                                    },
                                    suggestedMin: 0,
                                    suggestedMax: 5,
                                    ticks: { stepSize: 1, display: false }
                                }
                            },
                            plugins: {
                                legend: { display: false }
                            },
                            maintainAspectRatio: false
                        }
                    });
                },

                restart() {
                    this.step = 'intro';
                },
                
                shareResult() {
                    const text = `我的 AI 成熟度評估結果：\n🏆 ${this.result.title_zh}\n📊 狀態：${this.result.status}\n💡 核心特徵：${this.result.trait}\n\n這是一個基於 5 級 AI 成熟度模型的自我評估。`;
                    
                    // Copy to clipboard logic
                    if (navigator.clipboard && window.isSecureContext) {
                        navigator.clipboard.writeText(text).then(() => {
                            this.showToast = true;
                            setTimeout(() => this.showToast = false, 3000);
                        });
                    } else {
                        // Fallback
                        const textArea = document.createElement("textarea");
                        textArea.value = text;
                        document.body.appendChild(textArea);
                        textArea.select();
                        document.execCommand('copy');
                        document.body.removeChild(textArea);
                        this.showToast = true;
                        setTimeout(() => this.showToast = false, 3000);
                    }
                }
            }
        }
    </script>
</body>
</html>
