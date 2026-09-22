# Index.html  
<!DOCTYPE html>  
<html lang="en" class="dark">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">  
    <title>ReadLens - Smart Document Reader & AI Quiz</title>  
  
    <!-- PWA & Mobile Web App Capabilities -->  
    <meta name="apple-mobile-web-app-capable" content="yes">  
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">  
    <meta name="apple-mobile-web-app-title" content="ReadLens">  
    <meta name="mobile-web-app-capable" content="yes">  
    <meta name="theme-color" content="#0f172a">  
  
    <!-- Tailwind CSS -->  
    <script src="https://cdn.tailwindcss.com"></script>  
    <script>  
        tailwind.config = {  
            darkMode: 'class',  
            theme: {  
                extend: {  
                    colors: {  
                        brand: {  
                            50: '#f0f5ff',  
                            100: '#e0ebff',  
                            500: '#6366f1',  
                            600: '#4f46e5',  
                            700: '#4338ca',  
                            900: '#1e1b4b'  
                        }  
                    }  
                }  
            }  
        }  
    </script>  
  
    <!-- FontAwesome Icons -->  
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">  
  
    <!-- Google Fonts -->  
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">  
  
    <!-- Tesseract OCR Engine CDN -->  
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>  
  
    <style>  
        body {  
            font-family: 'Inter', sans-serif;  
            touch-action: manipulation;  
            -webkit-tap-highlight-color: transparent;  
        }  
  
        /* Reading highlight styling */  
        .highlight-sentence {  
            background-color: rgba(99, 102, 241, 0.35);  
            border-radius: 4px;  
            box-shadow: 0 0 10px rgba(99, 102, 241, 0.4);  
            transition: all 0.2s ease;  
        }  
  
        .highlight-word {  
            background-color: #f59e0b;  
            color: #0f172a !important;  
            font-weight: 700;  
            border-radius: 3px;  
            padding: 0 4px;  
            box-shadow: 0 0 10px rgba(245, 158, 11, 0.6);  
            transition: all 0.1s ease;  
        }  
  
        /* Custom scrollbars */  
        ::-webkit-scrollbar {  
            width: 6px;  
            height: 6px;  
        }  
        ::-webkit-scrollbar-track {  
            background: #0f172a;  
        }  
        ::-webkit-scrollbar-thumb {  
            background: #334155;  
            border-radius: 3px;  
        }  
        ::-webkit-scrollbar-thumb:hover {  
            background: #475569;  
        }  
    </style>  
</head>  
<body class="bg-slate-950 text-slate-100 min-h-screen flex flex-col justify-between selection:bg-indigo-500 selection:text-white">  
  
    <header class="sticky top-0 z-40 bg-slate-900/90 backdrop-blur-md border-b border-slate-800 px-4 py-3 shadow-md">  
        <div class="max-w-5xl mx-auto flex items-center justify-between">  
            <div class="flex items-center space-x-3">  
                <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-indigo-600 to-violet-500 flex items-center justify-center shadow-lg shadow-indigo-500/20">  
                    <i class="fa-solid fa-glasses text-white text-xl"></i>  
                </div>  
                <div>  
                    <h1 class="text-xl font-bold tracking-tight bg-clip-text text-transparent bg-gradient-to-r from-white via-slate-200 to-indigo-300">  
                        ReadLens  
                    </h1>  
                    <p class="text-xs text-slate-400 font-medium hidden sm:block">Smart Document Reader & AI Quiz</p>  
                </div>  
            </div>  
  
            <div class="flex items-center space-x-2">  
                <button id="btn-fullscreen" class="p-2.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-300 hover:text-white transition-colors border border-slate-700" title="Toggle Fullscreen">  
                    <i class="fa-solid fa-expand text-base"></i>  
                </button>  
                <button id="btn-settings" class="p-2.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-300 hover:text-white transition-colors border border-slate-700" title="Settings">  
                    <i class="fa-solid fa-gear text-base"></i>  
                </button>  
            </div>  
        </div>  
    </header>  
  
    <main class="flex-grow max-w-5xl w-full mx-auto p-4 md:p-6 space-y-6">  
  
        <nav class="grid grid-cols-3 gap-2 bg-slate-900/80 p-1.5 rounded-xl border border-slate-800 shadow-inner">  
            <button id="tab-scan" class="tab-btn py-2.5 px-3 rounded-lg text-sm font-semibold flex items-center justify-center space-x-2 transition-all bg-indigo-600 text-white shadow">  
                <i class="fa-solid fa-camera"></i>  
                <span class="hidden sm:inline">1. Scan / Upload</span>  
                <span class="sm:hidden">Scan</span>  
            </button>  
            <button id="tab-read" class="tab-btn py-2.5 px-3 rounded-lg text-sm font-semibold flex items-center justify-center space-x-2 transition-all text-slate-400 hover:text-slate-200">  
                <i class="fa-solid fa-book-open"></i>  
                <span class="hidden sm:inline">2. Read & Listen</span>  
                <span class="sm:hidden">Read</span>  
            </button>  
            <button id="tab-quiz" class="tab-btn py-2.5 px-3 rounded-lg text-sm font-semibold flex items-center justify-center space-x-2 transition-all text-slate-400 hover:text-slate-200">  
                <i class="fa-solid fa-brain"></i>  
                <span class="hidden sm:inline">3. AI Quiz</span>  
                <span class="sm:hidden">Quiz</span>  
            </button>  
        </nav>  
  
        <div id="toast-message" class="hidden p-4 rounded-xl border flex items-center justify-between transition-all duration-300">  
            <div class="flex items-center space-x-3">  
                <i id="toast-icon" class="fa-solid fa-circle-info text-lg"></i>  
                <span id="toast-text" class="text-sm font-medium"></span>  
            </div>  
            <button onclick="hideToast()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>  
        </div>  
  
        <section id="section-scan" class="tab-content space-y-6">  
            <div class="bg-slate-900 rounded-2xl p-6 border border-slate-800 shadow-xl space-y-5">  
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2 border-b border-slate-800 pb-4">  
                    <div>  
                        <h2 class="text-lg font-bold text-white flex items-center space-x-2">  
                            <i class="fa-solid fa-file-image text-indigo-400"></i>  
                            <span>Import Image or Capture Document</span>  
                        </h2>  
                        <p class="text-xs text-slate-400">Upload an image, snap with camera, or paste from clipboard</p>  
                    </div>  
                    <span id="ocr-badge" class="px-3 py-1 rounded-full text-xs font-semibold bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">  
                        <i class="fa-solid fa-check-circle mr-1"></i> Direct Browser OCR Ready  
                    </span>  
                </div>  
  
                <!-- Input Drop Zone -->  
                <div id="drop-zone" class="border-2 border-dashed border-slate-700 hover:border-indigo-500 bg-slate-950/50 hover:bg-indigo-950/10 rounded-xl p-6 text-center transition-all cursor-pointer relative group">  
                    <input type="file" id="file-input" accept="image/*" class="hidden">  
                    <input type="file" id="camera-input" accept="image/*" capture="environment" class="hidden">  
                      
                    <!-- Preview Container -->  
                    <div id="preview-container" class="hidden mb-4 relative max-w-xs mx-auto">  
                        <img id="image-preview" src="" alt="Document Preview" class="rounded-lg max-h-48 mx-auto shadow-md border border-slate-700">  
                        <button id="btn-remove-image" class="absolute -top-2 -right-2 bg-rose-600 hover:bg-rose-500 text-white rounded-full w-6 h-6 flex items-center justify-center text-xs shadow-lg">  
                            <i class="fa-solid fa-xmark"></i>  
                        </button>  
                    </div>  
  
                    <div id="upload-prompt" class="space-y-4">  
                        <div class="w-16 h-16 mx-auto rounded-full bg-slate-800 group-hover:bg-indigo-600/20 flex items-center justify-center transition-colors">  
                            <i class="fa-solid fa-cloud-arrow-up text-2xl text-slate-400 group-hover:text-indigo-400 transition-colors"></i>  
                        </div>  
                        <div>  
                            <p class="text-base font-semibold text-slate-200">Tap to upload, drag image, or paste from clipboard</p>  
                            <p class="text-xs text-slate-500 mt-1">Supports JPG, PNG, WEBP document photos</p>  
                        </div>  
                        <div class="flex flex-wrap justify-center gap-3 pt-2">  
                            <button type="button" id="btn-trigger-file" class="px-4 py-2 rounded-lg bg-indigo-600 hover:bg-indigo-500 text-xs font-semibold text-white shadow">  
                                <i class="fa-solid fa-folder-open mr-1.5"></i> Browse File  
                            </button>  
                            <button type="button" id="btn-trigger-camera" class="px-4 py-2 rounded-lg bg-slate-800 hover:bg-slate-700 text-xs font-semibold text-slate-200 border border-slate-700">  
                                <i class="fa-solid fa-camera mr-1.5"></i> Take Photo  
                            </button>  
                        </div>  
                    </div>  
                </div>  
  
                <!-- Progress Bar -->  
                <div id="ocr-progress-container" class="hidden space-y-2">  
                    <div class="flex justify-between text-xs font-medium text-slate-300">  
                        <span id="ocr-status-text">Scanning image text...</span>  
                        <span id="ocr-percentage">0%</span>  
                    </div>  
                    <div class="w-full h-2.5 bg-slate-800 rounded-full overflow-hidden">  
                        <div id="ocr-progress-bar" class="h-full bg-indigo-600 transition-all duration-200 w-0"></div>  
                    </div>  
                </div>  
  
                <!-- Manual Editor & Formatting Controls -->  
                <div class="pt-2 space-y-2">  
                    <div class="flex justify-between items-center">  
                        <label class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Document Text Editor</label>  
                        <div class="flex items-center space-x-3">  
                            <button id="btn-clean-spacing" class="text-xs text-emerald-400 hover:text-emerald-300 font-medium flex items-center space-x-1">  
                                <i class="fa-solid fa-wand-magic-sparkles"></i>  
                                <span>Clean Spacing</span>  
                            </button>  
                            <button id="btn-load-sample" class="text-xs text-indigo-400 hover:text-indigo-300 font-medium flex items-center space-x-1">  
                                <i class="fa-solid fa-file-lines"></i>  
                                <span>Load Sample</span>  
                            </button>  
                        </div>  
                    </div>  
                    <textarea id="document-text" rows="8" class="w-full bg-slate-950 border border-slate-800 rounded-xl p-4 text-slate-200 focus:outline-none focus:border-indigo-500 font-normal leading-relaxed resize-y text-sm" placeholder="Scanned document text will appear here automatically. You can also paste or type directly..."></textarea>  
                </div>  
  
                <div class="flex justify-end">  
                    <button id="btn-proceed-read" class="w-full sm:w-auto px-6 py-3 rounded-xl bg-indigo-600 hover:bg-indigo-500 font-semibold text-white transition-all shadow-lg shadow-indigo-600/25 flex items-center justify-center space-x-2">  
                        <span>Start Audio Reader</span>  
                        <i class="fa-solid fa-arrow-right"></i>  
                    </button>  
                </div>  
            </div>  
        </section>  
  
        <section id="section-read" class="tab-content space-y-6 hidden">  
            <div class="bg-slate-900 rounded-2xl p-6 border border-slate-800 shadow-xl space-y-6">  
                  
                <div class="flex flex-wrap items-center justify-between gap-4 pb-4 border-b border-slate-800">  
                    <div class="flex items-center space-x-2 w-full md:w-auto">  
                        <label class="text-xs font-semibold text-slate-400 uppercase tracking-wider whitespace-nowrap">Mode:</label>  
                        <select id="select-reading-mode" class="bg-slate-950 border border-slate-700 text-slate-200 text-xs font-semibold rounded-lg px-3 py-2 focus:outline-none focus:border-indigo-500 w-full md:w-auto">  
                            <option value="word">Word by Word</option>  
                            <option value="sentence" selected>Sentence Mode</option>  
                            <option value="whole">Whole Text Continuous</option>  
                        </select>  
                    </div>  
  
                    <div class="flex items-center space-x-3 w-full md:w-auto">  
                        <i class="fa-solid fa-gauge-simple-high text-slate-400 text-xs"></i>  
                        <span class="text-xs font-semibold text-slate-400 uppercase">Speed:</span>  
                        <input type="range" id="speech-speed" min="0.4" max="1.5" step="0.1" value="0.9" class="w-32 accent-indigo-500 cursor-pointer">  
                        <span id="speed-label" class="text-xs font-bold text-indigo-400 min-w-[2.5rem]">0.9x</span>  
                    </div>  
                </div>  
  
                <div class="flex flex-wrap items-center justify-center gap-3">  
                    <button id="btn-reset-reader" class="p-3 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 hover:text-white transition-all border border-slate-700" title="Reset to start">  
                        <i class="fa-solid fa-rotate-left"></i>  
                    </button>  
                    <button id="btn-prev" class="p-3 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 hover:text-white transition-all border border-slate-700" title="Previous Unit">  
                        <i class="fa-solid fa-backward-step"></i>  
                    </button>  
                    <button id="btn-play-pause" class="px-6 py-3.5 rounded-xl bg-indigo-600 hover:bg-indigo-500 text-white font-bold transition-all shadow-lg shadow-indigo-600/30 flex items-center space-x-2 min-w-[120px] justify-center">  
                        <i id="play-icon" class="fa-solid fa-play"></i>  
                        <span id="play-text">Play</span>  
                    </button>  
                    <button id="btn-next" class="p-3 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 hover:text-white transition-all border border-slate-700" title="Next Unit">  
                        <i class="fa-solid fa-forward-step"></i>  
                    </button>  
                </div>  
  
                <div class="relative bg-slate-950 border border-slate-800 rounded-xl p-6 min-h-[220px] max-h-[400px] overflow-y-auto leading-relaxed text-base md:text-lg text-slate-300 font-normal space-y-2 select-text" id="reader-display">  
                    <!-- Text rendered dynamically -->  
                </div>  
  
                <div class="flex justify-between items-center text-xs text-slate-500 pt-2">  
                    <span id="reader-stats">Words: 0 | Sentences: 0</span>  
                    <span id="reader-mode-indicator" class="font-medium text-indigo-400">Sentence Mode</span>  
                </div>  
            </div>  
        </section>  
  
        <section id="section-quiz" class="tab-content space-y-6 hidden">  
            <div class="bg-slate-900 rounded-2xl p-6 border border-slate-800 shadow-xl space-y-6">  
                  
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 pb-4 border-b border-slate-800">  
                    <div>  
                        <h2 class="text-lg font-bold text-white flex items-center space-x-2">  
                            <i class="fa-solid fa-lightbulb text-amber-400"></i>  
                            <span>AI Comprehension Quiz</span>  
                        </h2>  
                        <p class="text-xs text-slate-400">Generate multiple choice questions based on your document</p>  
                    </div>  
                    <button id="btn-generate-quiz" class="w-full sm:w-auto px-5 py-2.5 bg-gradient-to-r from-indigo-600 to-violet-600 hover:from-indigo-500 hover:to-violet-500 text-white text-sm font-semibold rounded-xl shadow-lg shadow-indigo-600/25 flex items-center justify-center space-x-2">  
                        <i class="fa-solid fa-sparkles"></i>  
                        <span>Generate Quiz</span>  
                    </button>  
                </div>  
  
                <div id="quiz-loading" class="hidden py-12 text-center space-y-4">  
                    <div class="inline-block animate-spin rounded-full h-10 w-10 border-4 border-indigo-500 border-t-transparent"></div>  
                    <p class="text-sm font-medium text-slate-300">Analyzing document and constructing quiz questions...</p>  
                </div>  
  
                <div id="quiz-empty" class="py-12 text-center space-y-3">  
                    <div class="w-16 h-16 mx-auto rounded-full bg-slate-800 flex items-center justify-center">  
                        <i class="fa-solid fa-clipboard-question text-2xl text-slate-500"></i>  
                    </div>  
                    <p class="text-slate-300 font-medium">No quiz generated yet</p>  
                    <p class="text-xs text-slate-500 max-w-md mx-auto">Click 'Generate Quiz' above to synthesize 3 comprehension questions with detailed explanations.</p>  
                </div>  
  
                <div id="quiz-container" class="hidden space-y-6">  
                    <div id="quiz-questions-list" class="space-y-6"></div>  
  
                    <div class="pt-4 border-t border-slate-800 flex flex-col sm:flex-row items-center justify-between gap-4">  
                        <div id="quiz-score-badge" class="hidden text-sm font-bold px-4 py-2 rounded-xl bg-indigo-500/10 text-indigo-400 border border-indigo-500/20">  
                            <!-- Score rendered here -->  
                        </div>  
                        <button id="btn-submit-quiz" class="w-full sm:w-auto px-6 py-3 bg-emerald-600 hover:bg-emerald-500 text-white font-semibold rounded-xl shadow-lg shadow-emerald-600/20 transition-all">  
                            Submit Answers  
                        </button>  
                    </div>  
                </div>  
  
            </div>  
        </section>  
  
    </main>  
  
    <div id="modal-settings" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">  
        <div class="bg-slate-900 border border-slate-800 rounded-2xl w-full max-w-md p-6 space-y-5 shadow-2xl">  
            <div class="flex justify-between items-center border-b border-slate-800 pb-3">  
                <h3 class="text-lg font-bold text-white flex items-center space-x-2">  
                    <i class="fa-solid fa-sliders text-indigo-400"></i>  
                    <span>ReadLens Settings</span>  
                </h3>  
                <button id="btn-close-settings" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>  
            </div>  
  
            <div class="space-y-4">  
                <div>  
                    <label class="block text-xs font-semibold text-slate-300 mb-1">Gemini API Key (Optional)</label>  
                    <input type="password" id="input-api-key" placeholder="AIZASy..." class="w-full bg-slate-950 border border-slate-700 rounded-xl px-4 py-2.5 text-sm text-slate-200 focus:outline-none focus:border-indigo-500">  
                    <p class="text-xs text-slate-500 mt-1.5">If omitted, ReadLens will use its smart offline rule engine for quiz generation.</p>  
                </div>  
            </div>  
  
            <div class="flex justify-end space-x-3 pt-3 border-t border-slate-800">  
                <button id="btn-save-settings" class="px-5 py-2.5 rounded-xl bg-indigo-600 hover:bg-indigo-500 font-semibold text-white text-sm transition-all shadow-lg shadow-indigo-600/20">  
                    Save Changes  
                </button>  
            </div>  
        </div>  
    </div>  
  
    <script>  
        const state = {  
            activeTab: 'scan',  
            apiKey: localStorage.getItem('readlens_gemini_key') || '',  
            documentText: '',  
            sentences: [],  
            words: [],  
            readingMode: 'sentence',  
            speed: 0.9,  
            isPlaying: false,  
            currentSentenceIndex: 0,  
            currentWordIndex: 0,  
            quizData: [],  
            userAnswers: {}  
        };  
  
        const synth = window.speechSynthesis;  
        let currentUtterance = null;  
        let wordAutoTimer = null;  
  
        const sampleText = `Artificial Intelligence is transforming how we learn and process written documents. By combining optical character recognition with high-quality text-to-speech technologies, readers can engage with content through multiple sensory channels. This dual-coding approach improves reading comprehension, speed, and retention. Whether you are studying complex research papers or listening to everyday articles, granular control over reading speed and sentence highlighting makes learning personalized and effortless.`;  
  
        document.addEventListener('DOMContentLoaded', () => {  
            initDOM();  
            initTabs();  
            initImageUploadAndOCR();  
            initReader();  
            initQuiz();  
            initSettings();  
            initFullscreen();  
        });  
  
        // Advanced Text Sanitization to prevent OCR word merging in TTS  
        function sanitizeTextSpacing(rawText) {  
            if (!rawText) return '';  
              
            return rawText  
                // Replace non-breaking spaces, zero-width spaces, and soft hyphens with normal ASCII spaces  
                .replace(/[\u00A0\u200B\u200C\u200D\u200E\u200F\uFEFF]/g, ' ')  
                // Reconnect hyphenated line-breaks (e.g. "infor-\nmation" -> "information")  
                .replace(/(\w+)-\s*[\r\n]+\s*(\w+)/g, '$1$2')  
                // Add space after punctuation if missing (e.g., "word.Next" -> "word. Next")  
                .replace(/([\.!\?,;:])/g, '$1 ')  
                // Add space between lowercase and uppercase letters (CamelCase concatenation fix: "theQuick" -> "the Quick")  
                .replace(/([a-z])([A-Z])/g, '$1 $2')  
                // Add space between letters and numbers (e.g., "Chapter1" -> "Chapter 1")  
                .replace(/([a-zA-Z])(\d)/g, '$1 $2')  
                .replace(/(\d)([a-zA-Z])/g, '$1 $2')  
                // Collapse multiple consecutive spaces or line breaks into single space  
                .replace(/\s+/g, ' ')  
                .trim();  
        }  
  
        function initDOM() {  
            document.getElementById('document-text').value = sampleText;  
            state.documentText = sampleText;  
            document.getElementById('input-api-key').value = state.apiKey;  
              
            document.getElementById('btn-load-sample').addEventListener('click', () => {  
                document.getElementById('document-text').value = sampleText;  
                state.documentText = sampleText;  
                showToast('Sample text loaded!', 'success');  
            });  
  
            document.getElementById('btn-clean-spacing').addEventListener('click', () => {  
                const currentVal = document.getElementById('document-text').value;  
                const cleaned = sanitizeTextSpacing(currentVal);  
                document.getElementById('document-text').value = cleaned;  
                state.documentText = cleaned;  
                showToast('Text spacing cleaned and normalized!', 'success');  
            });  
  
            document.getElementById('btn-proceed-read').addEventListener('click', () => {  
                const text = sanitizeTextSpacing(document.getElementById('document-text').value);  
                if (!text) {  
                    showToast('Please enter or scan text first.', 'error');  
                    return;  
                }  
                state.documentText = text;  
                switchTab('read');  
                prepareReaderText();  
            });  
        }  
  
        function showToast(message, type = 'info') {  
            const toast = document.getElementById('toast-message');  
            const icon = document.getElementById('toast-icon');  
            const text = document.getElementById('toast-text');  
  
            text.textContent = message;  
            toast.className = 'p-4 rounded-xl border flex items-center justify-between transition-all duration-300 ';  
  
            if (type === 'error') {  
                toast.classList.add('bg-rose-500/10', 'border-rose-500/30', 'text-rose-300');  
                icon.className = 'fa-solid fa-circle-exclamation text-rose-400';  
            } else if (type === 'success') {  
                toast.classList.add('bg-emerald-500/10', 'border-emerald-500/30', 'text-emerald-300');  
                icon.className = 'fa-solid fa-circle-check text-emerald-400';  
            } else {  
                toast.classList.add('bg-indigo-500/10', 'border-indigo-500/30', 'text-indigo-300');  
                icon.className = 'fa-solid fa-circle-info text-indigo-400';  
            }  
  
            toast.classList.remove('hidden');  
            setTimeout(() => toast.classList.add('hidden'), 4000);  
        }  
  
        function hideToast() {  
            document.getElementById('toast-message').classList.add('hidden');  
        }  
  
        function initTabs() {  
            const tabs = {  
                'tab-scan': 'section-scan',  
                'tab-read': 'section-read',  
                'tab-quiz': 'section-quiz'  
            };  
  
            Object.keys(tabs).forEach(tabId => {  
                document.getElementById(tabId).addEventListener('click', () => {  
                    const targetTabName = tabId.replace('tab-', '');  
                      
                    if (targetTabName === 'read' || targetTabName === 'quiz') {  
                        const rawText = sanitizeTextSpacing(document.getElementById('document-text').value);  
                        if (!rawText) {  
                            showToast('Please upload or enter text first.', 'error');  
                            return;  
                        }  
                        state.documentText = rawText;  
                        prepareReaderText();  
                    }  
  
                    switchTab(targetTabName);  
                });  
            });  
        }  
  
        function switchTab(tabName) {  
            state.activeTab = tabName;  
            ['scan', 'read', 'quiz'].forEach(t => {  
                const btn = document.getElementById(`tab-${t}`);  
                const sec = document.getElementById(`section-${t}`);  
                if (t === tabName) {  
                    btn.className = 'tab-btn py-2.5 px-3 rounded-lg text-sm font-semibold flex items-center justify-center space-x-2 transition-all bg-indigo-600 text-white shadow';  
                    sec.classList.remove('hidden');  
                } else {  
                    btn.className = 'tab-btn py-2.5 px-3 rounded-lg text-sm font-semibold flex items-center justify-center space-x-2 transition-all text-slate-400 hover:text-slate-200';  
                    sec.classList.add('hidden');  
                }  
            });  
        }  
  
        function initImageUploadAndOCR() {  
            const dropZone = document.getElementById('drop-zone');  
            const fileInput = document.getElementById('file-input');  
            const cameraInput = document.getElementById('camera-input');  
            const btnFile = document.getElementById('btn-trigger-file');  
            const btnCamera = document.getElementById('btn-trigger-camera');  
            const btnRemove = document.getElementById('btn-remove-image');  
  
            btnFile.addEventListener('click', (e) => { e.stopPropagation(); fileInput.click(); });  
            btnCamera.addEventListener('click', (e) => { e.stopPropagation(); cameraInput.click(); });  
  
            fileInput.addEventListener('change', handleFileSelect);  
            cameraInput.addEventListener('change', handleFileSelect);  
  
            btnRemove.addEventListener('click', (e) => {  
                e.stopPropagation();  
                document.getElementById('preview-container').classList.add('hidden');  
                document.getElementById('upload-prompt').classList.remove('hidden');  
                fileInput.value = '';  
                cameraInput.value = '';  
            });  
  
            dropZone.addEventListener('dragover', (e) => {  
                e.preventDefault();  
                dropZone.classList.add('border-indigo-500', 'bg-indigo-950/20');  
            });  
  
            dropZone.addEventListener('dragleave', () => {  
                dropZone.classList.remove('border-indigo-500', 'bg-indigo-950/20');  
            });  
  
            dropZone.addEventListener('drop', (e) => {  
                e.preventDefault();  
                dropZone.classList.remove('border-indigo-500', 'bg-indigo-950/20');  
                if (e.dataTransfer.files && e.dataTransfer.files[0]) {  
                    processImageFile(e.dataTransfer.files[0]);  
                }  
            });  
  
            window.addEventListener('paste', (e) => {  
                if (state.activeTab !== 'scan') return;  
                const items = (e.clipboardData || e.originalEvent.clipboardData).items;  
                for (let item of items) {  
                    if (item.type.indexOf('image') === 0) {  
                        const blob = item.getAsFile();  
                        processImageFile(blob);  
                        showToast('Pasted image from clipboard!', 'success');  
                        break;  
                    }  
                }  
            });  
        }  
  
        function handleFileSelect(e) {  
            if (e.target.files && e.target.files[0]) {  
                processImageFile(e.target.files[0]);  
            }  
        }  
  
        function processImageFile(file) {  
            if (!file.type.startsWith('image/')) {  
                showToast('Please upload a valid image file.', 'error');  
                return;  
            }  
  
            const reader = new FileReader();  
            reader.onload = (e) => {  
                document.getElementById('image-preview').src = e.target.result;  
                document.getElementById('preview-container').classList.remove('hidden');  
                document.getElementById('upload-prompt').classList.add('hidden');  
                preprocessAndRunOCR(e.target.result);  
            };  
            reader.readAsDataURL(file);  
        }  
  
        function preprocessAndRunOCR(dataUrl) {  
            const img = new Image();  
            img.onload = () => {  
                const canvas = document.createElement('canvas');  
                const ctx = canvas.getContext('2d');  
  
                const maxDim = 1600;  
                let width = img.width;  
                let height = img.height;  
  
                if (width > maxDim || height > maxDim) {  
                    if (width > height) {  
                        height = Math.round((height * maxDim) / width);  
                        width = maxDim;  
                    } else {  
                        width = Math.round((width * maxDim) / height);  
                        height = maxDim;  
                    }  
                }  
  
                canvas.width = width;  
                canvas.height = height;  
                ctx.drawImage(img, 0, 0, width, height);  
  
                runTesseractOCR(canvas);  
            };  
            img.src = dataUrl;  
        }  
  
        async function runTesseractOCR(canvas) {  
            const progressContainer = document.getElementById('ocr-progress-container');  
            const progressBar = document.getElementById('ocr-progress-bar');  
            const statusText = document.getElementById('ocr-status-text');  
            const percentageText = document.getElementById('ocr-percentage');  
  
            progressContainer.classList.remove('hidden');  
            progressBar.style.width = '0%';  
            statusText.textContent = 'Initializing engine...';  
  
            try {  
                const result = await Tesseract.recognize(  
                    canvas,  
                    'eng',  
                    {  
                        preserve_interword_spaces: '1',  
                        logger: m => {  
                            if (m.status === 'recognizing text') {  
                                const progress = Math.round((m.progress || 0) * 100);  
                                progressBar.style.width = `${progress}%`;  
                                percentageText.textContent = `${progress}%`;  
                                statusText.textContent = `Processing document text (${progress}%)...`;  
                            }  
                        }  
                    }  
                );  
  
                const scannedRaw = result.data.text.trim();  
                const cleanedText = sanitizeTextSpacing(scannedRaw);  
  
                if (cleanedText) {  
                    document.getElementById('document-text').value = cleanedText;  
                    state.documentText = cleanedText;  
                    showToast('Document scanned and formatted successfully!', 'success');  
                } else {  
                    showToast('No text detected. Try typing or pasting directly below.', 'info');  
                }  
  
            } catch (err) {  
                console.error('OCR error:', err);  
                showToast('OCR scan encountered an issue. You can edit/paste text directly.', 'error');  
            } finally {  
                setTimeout(() => progressContainer.classList.add('hidden'), 1000);  
            }  
        }  
  
        function initReader() {  
            const selectMode = document.getElementById('select-reading-mode');  
            const speedInput = document.getElementById('speech-speed');  
            const speedLabel = document.getElementById('speed-label');  
            const btnPlay = document.getElementById('btn-play-pause');  
            const btnPrev = document.getElementById('btn-prev');  
            const btnNext = document.getElementById('btn-next');  
            const btnReset = document.getElementById('btn-reset-reader');  
  
            selectMode.addEventListener('change', (e) => {  
                stopSpeech();  
                state.readingMode = e.target.value;  
                document.getElementById('reader-mode-indicator').textContent =   
                    state.readingMode === 'word' ? 'Word Mode' :  
                    state.readingMode === 'sentence' ? 'Sentence Mode' : 'Whole Text Mode';  
                renderTextDisplay();  
            });  
  
            speedInput.addEventListener('input', (e) => {  
                state.speed = parseFloat(e.target.value);  
                speedLabel.textContent = `${state.speed.toFixed(1)}x`;  
                if (state.isPlaying && state.readingMode === 'whole') {  
                    stopSpeech();  
                    playCurrentUnit();  
                }  
            });  
  
            btnPlay.addEventListener('click', togglePlayPause);  
            btnPrev.addEventListener('click', navigatePrevious);  
            btnNext.addEventListener('click', navigateNext);  
            btnReset.addEventListener('click', resetReader);  
        }  
  
        function prepareReaderText() {  
            stopSpeech();  
            const raw = sanitizeTextSpacing(state.documentText);  
              
            const sentenceMatches = raw.match(/[^.!?]+[.!?]+|[^.!?]+$/g) || [raw];  
            state.sentences = sentenceMatches.map(s => s.trim()).filter(s => s.length > 0);  
            state.words = raw.split(/\s+/).filter(w => w.length > 0);  
  
            state.currentSentenceIndex = 0;  
            state.currentWordIndex = 0;  
  
            document.getElementById('reader-stats').textContent =   
                `Words: ${state.words.length} | Sentences: ${state.sentences.length}`;  
  
            renderTextDisplay();  
        }  
  
        function renderTextDisplay() {  
            const display = document.getElementById('reader-display');  
            display.innerHTML = '';  
  
            if (state.sentences.length === 0) return;  
  
            let wordCounter = 0;  
  
            state.sentences.forEach((sentence, sIdx) => {  
                const sentSpan = document.createElement('span');  
                sentSpan.id = `sent-${sIdx}`;  
                sentSpan.className = 'inline cursor-pointer hover:bg-slate-800/60 rounded px-1 transition-colors';  
  
                const wordsInSent = sentence.split(/\s+/);  
                wordsInSent.forEach((word) => {  
                    const wSpan = document.createElement('span');  
                    wSpan.id = `word-${wordCounter}`;  
                    wSpan.textContent = word + ' ';  
                    wSpan.className = 'inline-block transition-all';  
                      
                    const localWordIdx = wordCounter;  
                    wSpan.addEventListener('click', (e) => {  
                        e.stopPropagation();  
                        jumpToWord(localWordIdx, sIdx);  
                    });  
  
                    sentSpan.appendChild(wSpan);  
                    wordCounter++;  
                });  
  
                sentSpan.addEventListener('click', () => jumpToSentence(sIdx));  
                display.appendChild(sentSpan);  
                display.appendChild(document.createTextNode(' '));  
            });  
  
            updateHighlights();  
        }  
  
        function updateHighlights() {  
            document.querySelectorAll('.highlight-sentence').forEach(el => el.classList.remove('highlight-sentence'));  
            document.querySelectorAll('.highlight-word').forEach(el => el.classList.remove('highlight-word'));  
  
            if (state.readingMode === 'sentence' || state.readingMode === 'whole') {  
                const activeSentence = document.getElementById(`sent-${state.currentSentenceIndex}`);  
                if (activeSentence) {  
                    activeSentence.classList.add('highlight-sentence');  
                    activeSentence.scrollIntoView({ behavior: 'smooth', block: 'center' });  
                }  
            } else if (state.readingMode === 'word') {  
                const activeWord = document.getElementById(`word-${state.currentWordIndex}`);  
                if (activeWord) {  
                    activeWord.classList.add('highlight-word');  
                    activeWord.scrollIntoView({ behavior: 'smooth', block: 'center' });  
                }  
            }  
        }  
  
        function togglePlayPause() {  
            if (state.isPlaying) {  
                pauseSpeech();  
            } else {  
                startSpeech();  
            }  
        }  
  
        function startSpeech() {  
            state.isPlaying = true;  
            updatePlayButtonUI(true);  
            playCurrentUnit();  
        }  
  
        function pauseSpeech() {  
            state.isPlaying = false;  
            stopSpeech();  
            updatePlayButtonUI(false);  
        }  
  
        function stopSpeech() {  
            if (synth.speaking || synth.pending) {  
                synth.cancel();  
            }  
            if (wordAutoTimer) {  
                clearTimeout(wordAutoTimer);  
                wordAutoTimer = null;  
            }  
        }  
  
        function resetReader() {  
            stopSpeech();  
            state.isPlaying = false;  
            state.currentSentenceIndex = 0;  
            state.currentWordIndex = 0;  
            updatePlayButtonUI(false);  
            updateHighlights();  
        }  
  
        function updatePlayButtonUI(playing) {  
            const icon = document.getElementById('play-icon');  
            const text = document.getElementById('play-text');  
            if (playing) {  
                icon.className = 'fa-solid fa-pause';  
                text.textContent = 'Pause';  
            } else {  
                icon.className = 'fa-solid fa-play';  
                text.textContent = 'Play';  
            }  
        }  
  
        function playCurrentUnit() {  
            stopSpeech();  
  
            if (!state.isPlaying) return;  
  
            if (state.readingMode === 'word') {  
                if (state.currentWordIndex >= state.words.length) {  
                    resetReader();  
                    return;  
                }  
                updateHighlights();  
                  
                const wordToRead = state.words[state.currentWordIndex].replace(/[^\w\s]/gi, '');  
                currentUtterance = new SpeechSynthesisUtterance(wordToRead);  
                currentUtterance.rate = state.speed;  
  
                currentUtterance.onend = () => {  
                    if (state.isPlaying) {  
                        const delay = Math.max(150, 500 / state.speed);  
                        wordAutoTimer = setTimeout(() => {  
                            state.currentWordIndex++;  
                            playCurrentUnit();  
                        }, delay);  
                    }  
                };  
  
                synth.speak(currentUtterance);  
  
            } else if (state.readingMode === 'sentence') {  
                if (state.currentSentenceIndex >= state.sentences.length) {  
                    resetReader();  
                    return;  
                }  
                updateHighlights();  
  
                const sentenceText = state.sentences[state.currentSentenceIndex];  
                currentUtterance = new SpeechSynthesisUtterance(sentenceText);  
                currentUtterance.rate = state.speed;  
  
                currentUtterance.onend = () => {  
                    if (state.isPlaying) {  
                        state.currentSentenceIndex++;  
                        playCurrentUnit();  
                    }  
                };  
  
                synth.speak(currentUtterance);  
  
            } else if (state.readingMode === 'whole') {  
                updateHighlights();  
                const remainingText = state.sentences.slice(state.currentSentenceIndex).join(' ');  
                currentUtterance = new SpeechSynthesisUtterance(remainingText);  
                currentUtterance.rate = state.speed;  
  
                currentUtterance.onboundary = (event) => {  
                    if (event.name === 'word') {  
                        const charIndex = event.charIndex;  
                        let accumulatedLength = 0;  
                        for (let i = state.currentSentenceIndex; i < state.sentences.length; i++) {  
                            accumulatedLength += state.sentences[i].length + 1;  
                            if (charIndex < accumulatedLength) {  
                                state.currentSentenceIndex = i;  
                                updateHighlights();  
                                break;  
                            }  
                        }  
                    }  
                };  
  
                currentUtterance.onend = () => {  
                    resetReader();  
                };  
  
                synth.speak(currentUtterance);  
            }  
        }  
  
        function navigateNext() {  
            if (state.readingMode === 'word') {  
                if (state.currentWordIndex < state.words.length - 1) {  
                    state.currentWordIndex++;  
                    updateHighlights();  
                    if (state.isPlaying) playCurrentUnit();  
                }  
            } else {  
                if (state.currentSentenceIndex < state.sentences.length - 1) {  
                    state.currentSentenceIndex++;  
                    updateHighlights();  
                    if (state.isPlaying) playCurrentUnit();  
                }  
            }  
        }  
  
        function navigatePrevious() {  
            if (state.readingMode === 'word') {  
                if (state.currentWordIndex > 0) {  
                    state.currentWordIndex--;  
                    updateHighlights();  
                    if (state.isPlaying) playCurrentUnit();  
                }  
            } else {  
                if (state.currentSentenceIndex > 0) {  
                    state.currentSentenceIndex--;  
                    updateHighlights();  
                    if (state.isPlaying) playCurrentUnit();  
                }  
            }  
        }  
  
        function jumpToSentence(sIdx) {  
            state.currentSentenceIndex = sIdx;  
            updateHighlights();  
            if (state.isPlaying) playCurrentUnit();  
        }  
  
        function jumpToWord(wIdx, sIdx) {  
            state.currentWordIndex = wIdx;  
            state.currentSentenceIndex = sIdx;  
            updateHighlights();  
            if (state.isPlaying) playCurrentUnit();  
        }  
  
        function initQuiz() {  
            document.getElementById('btn-generate-quiz').addEventListener('click', generateQuiz);  
            document.getElementById('btn-submit-quiz').addEventListener('click', submitQuiz);  
        }  
  
        async function generateQuiz() {  
            const rawText = state.documentText || document.getElementById('document-text').value.trim();  
            if (!rawText) {  
                showToast('Please upload or enter document text first.', 'error');  
                return;  
            }  
  
            document.getElementById('quiz-empty').classList.add('hidden');  
            document.getElementById('quiz-container').classList.add('hidden');  
            document.getElementById('quiz-loading').classList.remove('hidden');  
  
            state.userAnswers = {};  
            document.getElementById('quiz-score-badge').classList.add('hidden');  
  
            try {  
                if (state.apiKey) {  
                    state.quizData = await fetchGeminiQuiz(rawText, state.apiKey);  
                } else {  
                    state.quizData = generateLocalRuleQuiz(rawText);  
                }  
                renderQuiz();  
            } catch (err) {  
                console.warn("AI Quiz generation fallback activated:", err);  
                state.quizData = generateLocalRuleQuiz(rawText);  
                renderQuiz();  
                showToast('Generated quiz using local rules engine.', 'info');  
            } finally {  
                document.getElementById('quiz-loading').classList.add('hidden');  
            }  
        }  
  
        async function fetchGeminiQuiz(text, apiKey) {  
            const prompt = `Analyze the text below and generate 3 multiple-choice comprehension questions.  
Return ONLY a valid raw JSON array of objects with zero markdown formatting.  
JSON structure: [{"question": string, "options": [string, string, string, string], "correctIndex": number (0-3), "explanation": string}]  
  
Text snippet:  
"${text.substring(0, 3000)}"`;  
  
            const response = await fetch(`[https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`](https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=$%7BapiKey%7D%60), {  
                method: 'POST',  
                headers: { 'Content-Type': 'application/json' },  
                body: JSON.stringify({  
                    contents: [{ parts: [{ text: prompt }] }]  
                })  
            });  
  
            if (!response.ok) throw new Error('API Request Failed');  
  
            const data = await response.json();  
            const resText = data.candidates[0].content.parts[0].text;  
            const cleanJson = resText.replace(/```json/g, '').replace(/```/g, '').trim();  
            return JSON.parse(cleanJson);  
        }  
  
        function generateLocalRuleQuiz(text) {  
            const sentences = state.sentences.length > 0 ? state.sentences : text.split(/[.!?]/).filter(s => s.trim().length > 10);  
            const questions = [];  
  
            const words = text.match(/\b[A-Za-z]{5,}\b/g) || ['document', 'learning', 'reading', 'content'];  
            const keyWord = words[0] || 'Content';  
  
            questions.push({  
                question: `What primary topic is explored in this passage?`,  
                options: [  
                    `Key concepts and functions concerning ${keyWord}`,  
                    `Historical architectural standards in Southern Europe`,  
                    `Theoretical principles of organic chemistry`,  
                    `Financial portfolio management strategies`  
                ],  
                correctIndex: 0,  
                explanation: `The text focuses primarily on subject matter relating to ${keyWord}.`  
            });  
  
            const targetSentence = sentences[Math.floor(sentences.length / 2)] || text.substring(0, 100);  
            questions.push({  
                question: `Which of the following statements accurately reflects the text content?`,  
                options: [  
                    `"${targetSentence.substring(0, 70)}..."`,  
                    `The text entirely refutes modern educational methodologies.`,  
                    `The author implies no real-world application exists.`,  
                    `None of the stated points are relevant to learning.`  
                ],  
                correctIndex: 0,  
                explanation: `This direct sentence from the document confirms the key points made.`  
            });  
  
            questions.push({  
                question: `What is the overarching goal or benefit discussed in this document?`,  
                options: [  
                    `Improving reading comprehension, accessibility, and retention.`,  
                    `Restricting information access and limiting document usage.`,  
                    `Automating manual physical labor through mechanics.`,  
                    `Replacing written communication entirely with numerical data.`  
                ],  
                correctIndex: 0,  
                explanation: `The passage emphasizes tools and techniques that enhance document processing and learning.`  
            });  
  
            return questions;  
        }  
  
        function renderQuiz() {  
            const list = document.getElementById('quiz-questions-list');  
            list.innerHTML = '';  
  
            state.quizData.forEach((q, qIdx) => {  
                const card = document.createElement('div');  
                card.className = 'bg-slate-950 border border-slate-800 rounded-xl p-5 space-y-4';  
  
                const qTitle = document.createElement('h3');  
                qTitle.className = 'text-base font-semibold text-white flex space-x-2';  
                qTitle.innerHTML = `<span class="text-indigo-400 font-bold">${qIdx + 1}.</span> <span>${q.question}</span>`;  
                card.appendChild(qTitle);  
  
                const optionsGrid = document.createElement('div');  
                optionsGrid.className = 'grid grid-cols-1 sm:grid-cols-2 gap-3 pt-2';  
  
                q.options.forEach((opt, oIdx) => {  
                    const btn = document.createElement('button');  
                    btn.type = 'button';  
                    btn.className = 'opt-btn text-left p-3 rounded-lg border border-slate-800 bg-slate-900/60 hover:bg-slate-800 text-sm text-slate-300 transition-all flex items-center space-x-3';  
                    btn.innerHTML = `<span class="w-6 h-6 rounded-full bg-slate-800 flex items-center justify-center text-xs font-bold text-slate-400">${String.fromCharCode(65 + oIdx)}</span><span>${opt}</span>`;  
  
                    btn.addEventListener('click', () => {  
                        state.userAnswers[qIdx] = oIdx;  
                        card.querySelectorAll('.opt-btn').forEach(b => {  
                            b.className = 'opt-btn text-left p-3 rounded-lg border border-slate-800 bg-slate-900/60 hover:bg-slate-800 text-sm text-slate-300 transition-all flex items-center space-x-3';  
                        });  
                        btn.className = 'opt-btn text-left p-3 rounded-lg border-2 border-indigo-500 bg-indigo-950/30 text-sm font-medium text-white transition-all flex items-center space-x-3';  
                    });  
  
                    optionsGrid.appendChild(btn);  
                });  
  
                card.appendChild(optionsGrid);  
  
                const expBox = document.createElement('div');  
                expBox.id = `exp-${qIdx}`;  
                expBox.className = 'hidden p-3 rounded-lg bg-indigo-500/10 border border-indigo-500/20 text-xs text-indigo-300 mt-3';  
                expBox.innerHTML = `<strong>Explanation:</strong> ${q.explanation}`;  
                card.appendChild(expBox);  
  
                list.appendChild(card);  
            });  
  
            document.getElementById('quiz-container').classList.remove('hidden');  
        }  
  
        function submitQuiz() {  
            if (Object.keys(state.userAnswers).length < state.quizData.length) {  
                showToast('Please answer all questions before submitting.', 'error');  
                return;  
            }  
  
            let score = 0;  
            state.quizData.forEach((q, qIdx) => {  
                if (state.userAnswers[qIdx] === q.correctIndex) score++;  
                const expBox = document.getElementById(`exp-${qIdx}`);  
                if (expBox) expBox.classList.remove('hidden');  
            });  
  
            const scoreBadge = document.getElementById('quiz-score-badge');  
            scoreBadge.textContent = `Score: ${score} / ${state.quizData.length} (${Math.round((score / state.quizData.length) * 100)}%)`;  
            scoreBadge.classList.remove('hidden');  
  
            showToast(`Quiz completed! You scored ${score} out of ${state.quizData.length}.`, 'success');  
        }  
  
        function initSettings() {  
            const modal = document.getElementById('modal-settings');  
            const btnOpen = document.getElementById('btn-settings');  
            const btnClose = document.getElementById('btn-close-settings');  
            const btnSave = document.getElementById('btn-save-settings');  
  
            btnOpen.addEventListener('click', () => modal.classList.remove('hidden'));  
            btnClose.addEventListener('click', () => modal.classList.add('hidden'));  
  
            btnSave.addEventListener('click', () => {  
                const key = document.getElementById('input-api-key').value.trim();  
                state.apiKey = key;  
                localStorage.setItem('readlens_gemini_key', key);  
                modal.classList.add('hidden');  
                showToast('Settings saved successfully.', 'success');  
            });  
        }  
  
        function initFullscreen() {  
            const btnFullscreen = document.getElementById('btn-fullscreen');  
            btnFullscreen.addEventListener('click', () => {  
                if (!document.fullscreenElement) {  
                    if (document.documentElement.requestFullscreen) {  
                        document.documentElement.requestFullscreen();  
                    } else if (document.documentElement.webkitRequestFullscreen) {  
                        document.documentElement.webkitRequestFullscreen();  
                    } else {  
                        showToast('To view full screen on iOS, tap Share -> Add to Home Screen.', 'info');  
                    }  
                } else {  
                    if (document.exitFullscreen) {  
                        document.exitFullscreen();  
                    }  
                }  
            });  
        }  
    </script>  
</body>  
</html>  
