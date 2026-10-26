<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ParcelGo - On-Demand Delivery & API Console</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@300;400;500;600;700&family=Inter:wght@300;400;500;600;700&display=swap');
        
        body {
            font-family: 'Hind Siliguri', 'Inter', sans-serif;
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #1e293b;
        }
        ::-webkit-scrollbar-thumb {
            background: #475569;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #64748b;
        }

        .pulse-badge {
            animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: .5; }
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col">

    <!-- Header / Navbar -->
    <header class="bg-slate-800 border-b border-slate-700 sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <!-- Logo & Brand -->
                <div class="flex items-center space-x-3">
                    <div class="bg-gradient-to-tr from-red-600 to-orange-500 p-2.5 rounded-xl shadow-lg shadow-red-500/20">
                        <i data-lucide="package shadow-inner text-white w-6 h-6"></i>
                    </div>
                    <div>
                        <span class="text-xl font-bold bg-gradient-to-r from-white via-slate-200 to-slate-400 bg-clip-text text-transparent">ParcelGo</span>
                        <span class="text-xs bg-red-500/20 text-red-400 px-2 py-0.5 rounded-full border border-red-500/30 ml-2 font-mono">On-Demand API v1</span>
                    </div>
                </div>

                <!-- Nav Tabs -->
                <nav class="hidden md:flex space-x-1">
                    <button onclick="switchTab('create-order')" id="nav-create-order" class="nav-tab px-4 py-2 rounded-lg text-sm font-medium transition-all flex items-center space-x-2 text-white bg-slate-700 shadow-sm">
                        <i data-lucide="plus-circle" class="w-4 h-4 text-red-400"></i>
                        <span>পার্সেল পাঠান</span>
                    </button>
                    <button onclick="switchTab('active-orders')" id="nav-active-orders" class="nav-tab px-4 py-2 rounded-lg text-sm font-medium transition-all flex items-center space-x-2 text-slate-300 hover:bg-slate-700/50">
                        <i data-lucide="list-ordered" class="w-4 h-4 text-emerald-400"></i>
                        <span>অর্ডার লিস্ট & ট্র্যাকিং</span>
                        <span id="order-count-badge" class="bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 text-xs px-2 py-0.5 rounded-full font-bold">2</span>
                    </button>
                    <button onclick="switchTab('payout-methods')" id="nav-payout-methods" class="nav-tab px-4 py-2 rounded-lg text-sm font-medium transition-all flex items-center space-x-2 text-slate-300 hover:bg-slate-700/50">
                        <i data-lucide="wallet" class="w-4 h-4 text-pink-400"></i>
                        <span>bKash পেআউট</span>
                    </button>
                </nav>

                <!-- Auth & API Console Toggle -->
                <div class="flex items-center space-x-3">
                    <div class="hidden sm:flex items-center space-x-2 bg-slate-900/80 px-3 py-1.5 rounded-full border border-slate-700 text-xs font-mono">
                        <span class="w-2 h-2 rounded-full bg-emerald-400 pulse-badge"></span>
                        <span class="text-slate-400">Bearer Token:</span>
                        <span class="text-emerald-400 truncate max-w-[100px]" title="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...">eyJhbGciOi...</span>
                    </div>

                    <button onclick="toggleApiConsole()" class="bg-indigo-600/20 hover:bg-indigo-600/30 text-indigo-400 border border-indigo-500/30 px-3 py-1.5 rounded-lg text-xs font-medium flex items-center space-x-1.5 transition-colors">
                        <i data-lucide="terminal" class="w-4 h-4"></i>
                        <span class="hidden sm:inline">API payloads</span>
                    </button>
                </div>
            </div>
        </div>
        
        <!-- Mobile Navigation Bar -->
        <div class="md:hidden flex border-t border-slate-700 bg-slate-800/95 backdrop-blur px-2 py-2 justify-around text-xs">
            <button onclick="switchTab('create-order')" class="flex flex-col items-center py-1 text-red-400">
                <i data-lucide="plus-circle" class="w-5 h-5 mb-0.5"></i>
                <span>পাঠান</span>
            </button>
            <button onclick="switchTab('active-orders')" class="flex flex-col items-center py-1 text-slate-400">
                <i data-lucide="list-ordered" class="w-5 h-5 mb-0.5"></i>
                <span>অর্ডারসমূহ</span>
            </button>
            <button onclick="switchTab('payout-methods')" class="flex flex-col items-center py-1 text-slate-400">
                <i data-lucide="wallet" class="w-5 h-5 mb-0.5"></i>
                <span>bKash</span>
            </button>
        </div>
    </header>

    <!-- Main Content Container -->
    <main class="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 grid grid-cols-1 lg:grid-cols-12 gap-6">

        <!-- Left / Center Section (Main Form & Interactive Tabs) -->
        <section id="main-content" class="lg:col-span-8 space-y-6">

            <!-- TAB 1: CREATE PARCEL ORDER -->
            <div id="tab-create-order" class="tab-page space-y-6">
                <!-- Banner -->
                <div class="bg-gradient-to-r from-red-900/40 via-slate-800 to-orange-900/30 p-6 rounded-2xl border border-red-500/20 shadow-xl relative overflow-hidden">
                    <div class="relative z-10 max-w-xl">
                        <h1 class="text-2xl font-bold text-white mb-2">অন-ডিমান্ড পার্সেল ও ফুড ডেলিভারি বুকিং</h1>
                        <p class="text-slate-300 text-sm leading-relaxed">১-২০ পর্যন্ত এপিআই এন্ডপয়েন্ট কানেক্টেড। পিকআপ ও ডেলিভারি তথ্য দিয়ে দ্রুত পার্সেল ডেলিভারি তৈরি করুন।</p>
                    </div>
                </div>

                <!-- Category Selection Grid (API Endpoints 5 & 6) -->
                <div class="bg-slate-800/60 rounded-2xl p-6 border border-slate-700/80 shadow-lg">
                    <div class="flex items-center justify-between mb-4">
                        <div>
                            <h2 class="text-lg font-semibold text-white flex items-center gap-2">
                                <i data-lucide="grid" class="w-5 h-5 text-red-400"></i>
                                আইটেম ক্যাটাগরি নির্বাচন করুন
                            </h2>
                            <p class="text-xs text-slate-400 mt-0.5">GET /api/v1/ondemand/parcels/categories (Endpoint #5)</p>
                        </div>
                        <span class="text-xs bg-slate-700 text-slate-300 px-2.5 py-1 rounded-full font-mono" id="selected-cat-badge">Selected: Homemade Food</span>
                    </div>

                    <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-5 gap-3" id="category-grid">
                        <!-- JS Dynamic Insertion -->
                    </div>
                </div>

                <!-- Parcel Booking Form (API Endpoints 7 & 8) -->
                <form id="parcel-form" onsubmit="handleCreateParcel(event)" class="bg-slate-800/60 rounded-2xl p-6 border border-slate-700/80 shadow-lg space-y-6">
                    <div class="border-b border-slate-700/80 pb-3 flex justify-between items-center">
                        <h2 class="text-lg font-semibold text-white flex items-center gap-2">
                            <i data-lucide="map-pin" class="w-5 h-5 text-orange-400"></i>
                            পিকআপ ও ডেলিভারি বিস্তারিত
                        </h2>
                        <span class="text-xs text-slate-400">POST /api/v1/ondemand/parcels (Endpoint #7)</span>
                    </div>

                    <!-- Pickup Details -->
                    <div class="space-y-4">
                        <h3 class="text-sm font-semibold text-slate-300 uppercase tracking-wider text-xs flex items-center gap-1.5">
                            <span class="w-2 h-2 rounded-full bg-red-400"></span> পিকআপ ইনফরমেশন (Sender)
                        </h3>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-xs font-medium text-slate-300 mb-1">প্রেরকের নাম</label>
                                <input type="text" id="pickup_name" value="Imran" required class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500 transition-colors">
                            </div>
                            <div>
                                <label class="block text-xs font-medium text-slate-300 mb-1">ফোন নম্বর</label>
                                <input type="text" id="pickup_phone" value="01712345678" required class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500 transition-colors">
                            </div>
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-300 mb-1">পিকআপ এড্রেস</label>
                            <input type="text" id="pickup_address" value="House 42, Road 7, Dhanmondi, Dhaka" required class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500 transition-colors">
                        </div>
                        <div class="grid grid-cols-2 md:grid-cols-4 gap-3">
                            <div>
                                <label class="block text-xs font-medium text-slate-400 mb-1">Latitude</label>
                                <input type="text" id="pickup_lat" value="23.7461" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-1.5 text-xs text-slate-300 font-mono">
                            </div>
                            <div>
                                <label class="block text-xs font-medium text-slate-400 mb-1">Longitude</label>
                                <input type="text" id="pickup_lng" value="90.3742" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-1.5 text-xs text-slate-300 font-mono">
                            </div>
                            <div>
                                <label class="block text-xs font-medium text-slate-400 mb-1">রেডি সময় (মিনিট)</label>
                                <input type="number" id="ready_in" value="15" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-1.5 text-xs text-slate-300">
                            </div>
                            <div>
                                <label class="block text-xs font-medium text-slate-400 mb-1">ডেডলাইন (মিনিট)</label>
                                <input type="number" id="deadline" value="60" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-1.5 text-xs text-slate-300">
                            </div>
                        </div>
                    </div>

                    <!-- Item Details -->
                    <div class="space-y-4 pt-4 border-t border-slate-700/60">
                        <h3 class="text-sm font-semibold text-slate-300 uppercase tracking-wider text-xs flex items-center gap-1.5">
                            <span class="w-2 h-2 rounded-full bg-amber-400"></span> আইটেমের বিস্তারিত
                        </h3>
                        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                            <div>
                                <label class="block text-xs font-medium text-slate-300 mb-1">আইটেমের নাম</label>
                                <input type="text" id="item_name" value="Cheesy Pepperoni Pizza" required class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500">
                            </div>
                            <div>
                                <label class="block text-xs font-medium text-slate-300 mb-1">পরিমাণ (Quantity)</label>
                                <input type="number" id="item_quantity" value="1" min="1" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500">
                            </div>
                            <div>
                                <label class="block text-xs font-medium text-slate-300 mb-1">আনুমানিক মূল্য (৳)</label>
                                <input type="number" id="item_price" value="1200" onchange="calculateCOD()" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500">
                            </div>
                        </div>
                    </div>

                    <!-- Delivery & Receiver Details -->
                    <div class="space-y-4 pt-4 border-t border-slate-700/60">
                        <h3 class="text-sm font-semibold text-slate-300 uppercase tracking-wider text-xs flex items-center gap-1.5">
                            <span class="w-2 h-2 rounded-full bg-emerald-400"></span> প্রাপকের তথ্য (Receiver)
                        </h3>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-xs font-medium text-slate-300 mb-1">প্রাপকের নাম</label>
                                <input type="text" id="receiver_name" value="Tanvir Ahmed" required class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500">
                            </div>
                            <div>
                                <label class="block text-xs font-medium text-slate-300 mb-1">প্রাপকের ফোন</label>
                                <input type="text" id="receiver_phone" value="01888990011" required class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500">
                            </div>
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-300 mb-1">ডেলিভারি ঠিকানা</label>
                            <input type="text" id="receiver_address" value="Mirpur 12 Bus Stand, Begum Rokeya Avenue, Dhaka" required class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500">
                        </div>
                    </div>

                    <!-- Payment / COD Options -->
                    <div class="space-y-4 pt-4 border-t border-slate-700/60">
                        <h3 class="text-sm font-semibold text-slate-300 uppercase tracking-wider text-xs flex items-center gap-1.5">
                            <span class="w-2 h-2 rounded-full bg-indigo-400"></span> পেমেন্ট ও ক্যাশ-অন-ডেলিভারি (COD)
                        </h3>
                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div class="bg-slate-900/50 p-3 rounded-xl border border-slate-700/60">
                                <label class="block text-xs font-medium text-slate-400 mb-2">পেমেন্ট কে করবে?</label>
                                <div class="flex items-center space-x-4">
                                    <label class="inline-flex items-center text-xs text-slate-200 cursor-pointer">
                                        <input type="radio" name="pay_type" value="receiver" checked class="form-radio text-red-500 bg-slate-800 border-slate-600 focus:ring-0">
                                        <span class="ml-2">প্রাপক (Receiver Pay)</span>
                                    </label>
                                    <label class="inline-flex items-center text-xs text-slate-200 cursor-pointer">
                                        <input type="radio" name="pay_type" value="sender" class="form-radio text-red-500 bg-slate-800 border-slate-600 focus:ring-0">
                                        <span class="ml-2">প্রেরক (Sender Pay)</span>
                                    </label>
                                </div>
                            </div>
                            <div class="bg-slate-900/50 p-3 rounded-xl border border-slate-700/60 flex items-center justify-between">
                                <div>
                                    <span class="block text-xs text-slate-400">মোট COD কালেকশন</span>
                                    <span class="text-lg font-bold text-emerald-400" id="display-total-cod">৳1200.00</span>
                                </div>
                                <div class="text-right">
                                    <span class="block text-xs text-slate-400">ডেলিভারি চার্জ</span>
                                    <span class="text-sm font-semibold text-slate-200">৳114.26</span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Submit Button -->
                    <div class="pt-2">
                        <button type="submit" class="w-full bg-gradient-to-r from-red-600 to-orange-500 hover:from-red-500 hover:to-orange-400 text-white font-semibold py-3.5 px-6 rounded-xl shadow-lg shadow-red-500/25 flex items-center justify-center space-x-2 transition-all transform hover:-translate-y-0.5">
                            <i data-lucide="send" class="w-5 h-5"></i>
                            <span>অর্ডার সাবমিট করুন (POST /parcels)</span>
                        </button>
                    </div>
                </form>
            </div>

            <!-- TAB 2: ACTIVE ORDERS & TRACKING -->
            <div id="tab-active-orders" class="tab-page hidden space-y-6">
                <div class="flex items-center justify-between">
                    <div>
                        <h2 class="text-xl font-bold text-white">লাইভ অর্ডার ম্যানেজমেন্ট & ট্র্যাকিং</h2>
                        <p class="text-xs text-slate-400">Endpoints #9, #10, #11, #12 (Active, Details, Cancel & Status)</p>
                    </div>
                    <button onclick="renderOrders()" class="bg-slate-800 hover:bg-slate-700 text-slate-300 border border-slate-700 px-3 py-1.5 rounded-lg text-xs flex items-center gap-1.5">
                        <i data-lucide="refresh-cw" class="w-3.5 h-3.5"></i>
                        <span>রিফ্রেশ লিস্ট</span>
                    </button>
                </div>

                <!-- Orders Container -->
                <div id="orders-list-container" class="space-y-4">
                    <!-- JS Injected Order Cards -->
                </div>
            </div>

            <!-- TAB 3: BKASH PAYOUT METHODS -->
            <div id="tab-payout-methods" class="tab-page hidden space-y-6">
                <div class="bg-slate-800/60 rounded-2xl p-6 border border-slate-700/80 shadow-lg space-y-6">
                    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 border-b border-slate-700 pb-4">
                        <div>
                            <h2 class="text-lg font-semibold text-white flex items-center gap-2">
                                <div class="w-6 h-6 rounded-full bg-pink-500/20 text-pink-400 flex items-center justify-center font-bold text-xs">b</div>
                                bKash পেআউট মেথড তালিকা
                            </h2>
                            <p class="text-xs text-slate-400">Endpoints #13, #14, #15, #16, #17, #18, #19, #20</p>
                        </div>
                        <button onclick="openAddBkashModal()" class="bg-pink-600 hover:bg-pink-500 text-white px-4 py-2 rounded-xl text-xs font-semibold flex items-center gap-2 shadow-lg shadow-pink-600/20 transition-all">
                            <i data-lucide="plus" class="w-4 h-4"></i>
                            <span>নতুন bKash অ্যাকাউন্ট যুক্ত করুন</span>
                        </button>
                    </div>

                    <!-- Payout Methods List -->
                    <div id="payout-methods-container" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <!-- JS Injected Cards -->
                    </div>
                </div>
            </div>

        </section>

        <!-- Right Side Panel: Interactive API Console & Payload Preview -->
        <aside id="api-console-panel" class="lg:col-span-4 space-y-6">
            <div class="bg-slate-950 rounded-2xl border border-slate-800 p-5 shadow-2xl sticky top-20">
                <div class="flex items-center justify-between border-b border-slate-800 pb-3">
                    <div class="flex items-center space-x-2">
                        <div class="w-3 h-3 rounded-full bg-red-500"></div>
                        <div class="w-3 h-3 rounded-full bg-yellow-500"></div>
                        <div class="w-3 h-3 rounded-full bg-green-500"></div>
                        <span class="text-xs font-mono text-slate-400 ml-2">Live API Inspector</span>
                    </div>
                    <span id="api-status-tag" class="text-[10px] font-mono bg-emerald-500/10 text-emerald-400 border border-emerald-500/20 px-2 py-0.5 rounded">200 OK</span>
                </div>

                <!-- API Details Header -->
                <div class="mt-4 space-y-2">
                    <div class="flex items-center space-x-2">
                        <span id="api-method" class="px-2 py-0.5 text-xs font-bold font-mono rounded bg-blue-500/20 text-blue-400 border border-blue-500/30">GET</span>
                        <span id="api-endpoint" class="text-xs font-mono text-slate-300 truncate font-semibold">/api/v1/ondemand/parcels/categories</span>
                    </div>
                    <p id="api-desc" class="text-xs text-slate-400 italic">পার্সেলের জন্য পাওয়া ক্যাটাগরিগুলো ফেচ করা হচ্ছে...</p>
                </div>

                <!-- Code / Payload Viewer -->
                <div class="mt-4 space-y-3">
                    <div class="flex items-center justify-between">
                        <span class="text-[11px] font-mono uppercase text-slate-400">Request & Response Body</span>
                        <button onclick="copyApiPayload()" class="text-xs text-slate-400 hover:text-white flex items-center gap-1">
                            <i data-lucide="copy" class="w-3.5 h-3.5"></i>
                            <span>Copy</span>
                        </button>
                    </div>
                    <div class="bg-slate-900 rounded-xl p-3 border border-slate-800 text-[11px] font-mono overflow-x-auto text-emerald-300 max-h-[420px]">
                        <pre id="api-payload-code" class="whitespace-pre-wrap leading-relaxed">// Click any button or action in UI to inspect cURL & JSON response payloads</pre>
                    </div>
                </div>

                <!-- Auth & Header preview -->
                <div class="mt-4 pt-3 border-t border-slate-800 text-[11px] font-mono text-slate-400 space-y-1">
                    <div>Authorization: <span class="text-slate-200">Bearer {{access_token}}</span></div>
                    <div>Content-Type: <span class="text-slate-200">application/json</span></div>
                </div>
            </div>
        </aside>

    </main>

    <!-- Modal: Add bKash Account -->
    <div id="bkash-modal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
        <div class="bg-slate-800 border border-slate-700 rounded-2xl w-full max-w-md p-6 shadow-2xl relative space-y-4">
            <button onclick="closeAddBkashModal()" class="absolute top-4 right-4 text-slate-400 hover:text-white">
                <i data-lucide="x" class="w-5 h-5"></i>
            </button>
            
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-xl bg-pink-500/20 text-pink-400 flex items-center justify-center font-bold text-lg">b</div>
                <div>
                    <h3 class="text-lg font-bold text-white">bKash পেআউট অ্যাকাউন্ট যুক্ত করুন</h3>
                    <p class="text-xs text-slate-400">Endpoint #14 (POST /payout-methods)</p>
                </div>
            </div>

            <form onsubmit="handleAddBkash(event)" class="space-y-4 pt-2">
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">অ্যাাকাউন্ট টাইপ</label>
                    <select id="modal_bkash_type" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-pink-500">
                        <option value="PERSONAL">PERSONAL</option>
                        <option value="MERCHANT">MERCHANT</option>
                    </select>
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">bKash মোবাইল নম্বর</label>
                    <input type="text" id="modal_bkash_phone" placeholder="01767191651" required class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-pink-500">
                </div>
                <div class="pt-2 flex gap-3">
                    <button type="button" onclick="closeAddBkashModal()" class="w-1/2 bg-slate-700 hover:bg-slate-600 text-slate-200 py-2.5 rounded-xl text-xs font-semibold">বাতিল</button>
                    <button type="submit" class="w-1/2 bg-pink-600 hover:bg-pink-500 text-white py-2.5 rounded-xl text-xs font-semibold shadow-lg shadow-pink-600/30">সেভ করুন</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal: Cancel Order Reason -->
    <div id="cancel-modal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
        <div class="bg-slate-800 border border-slate-700 rounded-2xl w-full max-w-md p-6 shadow-2xl relative space-y-4">
            <button onclick="closeCancelModal()" class="absolute top-4 right-4 text-slate-400 hover:text-white">
                <i data-lucide="x" class="w-5 h-5"></i>
            </button>
            
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-xl bg-red-500/20 text-red-400 flex items-center justify-center">
                    <i data-lucide="alert-triangle" class="w-6 h-6"></i>
                </div>
                <div>
                    <h3 class="text-lg font-bold text-white">অর্ডার বাতিল নিশ্চিতকরণ</h3>
                    <p class="text-xs text-slate-400">Endpoint #11 (POST /parcels/{id}/cancel)</p>
                </div>
            </div>

            <div class="space-y-3 pt-2">
                <input type="hidden" id="cancel_order_id">
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">বাতিলের কারণ বাছাই করুন</label>
                    <select id="cancel_reason" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-red-500">
                        <option value="customer-cancelled-order">গ্রাহক নিজে অর্ডার বাতিল করেছেন (customer-cancelled-order)</option>
                        <option value="rider-taking-too-long">রাইডার আসতে দেরি করছেন</option>
                        <option value="wrong-address">ভুল এড্রেস এন্ট্রি করা হয়েছে</option>
                    </select>
                </div>
                <div class="pt-2 flex gap-3">
                    <button onclick="closeCancelModal()" class="w-1/2 bg-slate-700 hover:bg-slate-600 text-slate-200 py-2.5 rounded-xl text-xs font-semibold">ফিরুন</button>
                    <button onclick="submitCancelOrder()" class="w-1/2 bg-red-600 hover:bg-red-500 text-white py-2.5 rounded-xl text-xs font-semibold shadow-lg shadow-red-600/30">হ্যাঁ, বাতিল করুন</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Toast Notification -->
    <div id="toast" class="fixed bottom-6 right-6 z-50 transform translate-y-20 opacity-0 transition-all duration-300 pointer-events-none flex items-center space-x-3 bg-slate-800 border border-slate-700 text-white px-4 py-3 rounded-xl shadow-2xl">
        <div id="toast-icon" class="text-emerald-400">
            <i data-lucide="check-circle" class="w-5 h-5"></i>
        </div>
        <div id="toast-msg" class="text-xs font-medium">সফলভাবে প্রসেস সম্পন্ন হয়েছে!</div>
    </div>

    <!-- Application State & Script -->
    <script>
        // Data Mock Store from 20 Endpoint Specs
        const state = {
            categories: [
                { id: "homemade_food", name: "Homemade Food", icon: "utensils", desc: "ঘরের তৈরি খাবার" },
                { id: "clothes", name: "Clothes", icon: "shirt", desc: "পোশাক ও পরিধেয়" },
                { id: "documents", name: "Documents", icon: "file-text", desc: "গুরুত্বপূর্ণ কাগজপত্র" },
                { id: "gifts", name: "Gifts", icon: "gift", desc: "উপহারসামগ্রী" },
                { id: "cosmetics", name: "Cosmetics", icon: "sparkles", desc: "প্রসাধনসামগ্রী" },
                { id: "medicine", name: "Medicine", icon: "pill", desc: "জরুরি ওষুধপত্র" },
                { id: "accessories", name: "Accessories", icon: "watch", desc: "ঘড়ি, চশমা ইত্যাদি" },
                { id: "perishable", name: "Perishable", icon: "apple", desc: "দ্রুত পচনশীল" },
                { id: "electronics", name: "Electronics", icon: "tv", desc: "গ্যাজেট ও ডিভাইসেস" },
                { id: "other", name: "Other Items", icon: "package", desc: "অন্যান্য সামগ্রী" }
            ],
            selectedCategory: "homemade_food",
            orders: [
                {
                    id: "4LHV2YB",
                    hash_id: "z1ci99jizevc",
                    status: "PICKED",
                    item_name: "Cheesy Pepperoni Pizza",
                    receiver: "Tanvir Ahmed",
                    phone: "01888990011",
                    address: "Mirpur 12 Bus Stand, Dhaka",
                    amount: "1200.00",
                    delivery_fee: "114.26",
                    rider: {
                        name: "Asibul H Hasan",
                        phone: "01767191651",
                        vehicle: "Bike DHK METRO HA 55-5956",
                        rating: "4.9"
                    },
                    proof_images: [
                        "https://placehold.co/400x300/1e293b/emerald?text=Pickup+Proof",
                        "https://placehold.co/400x300/1e293b/indigo?text=Item+Photo"
                    ]
                },
                {
                    id: "8KTY7XZ",
                    hash_id: "m9kl22aebx9",
                    status: "DELIVERED",
                    item_name: "Official Documents Folder",
                    receiver: "Rahim Uddin",
                    phone: "01555112233",
                    address: "Gulshan 2 Circle, Dhaka",
                    amount: "0.00",
                    delivery_fee: "80.00",
                    rider: {
                        name: "Kamrul Islam",
                        phone: "01911223344",
                        vehicle: "Bike DHK METRO LA 12-3456",
                        rating: "5.0"
                    },
                    proof_images: [
                        "https://placehold.co/400x300/1e293b/emerald?text=Delivery+Proof"
                    ]
                }
            ],
            payoutMethods: [
                {
                    id: "pm_bkash_9981",
                    gateway: "bKash",
                    account_type: "PERSONAL",
                    account_number: "01767191651",
                    is_default: true,
                    created_at: "2026-10-20"
                },
                {
                    id: "pm_bkash_3342",
                    gateway: "bKash",
                    account_type: "MERCHANT",
                    account_number: "01511223349",
                    is_default: false,
                    created_at: "2026-10-22"
                }
            ]
        };

        // Initialize App
        window.onload = function() {
            lucide.createIcons();
            renderCategories();
            renderOrders();
            renderPayoutMethods();
            showApiPayload('GET', '/api/v1/ondemand/parcels/categories', 'Endpoints #5 & #6: পার্সেল ক্যাটাগরি তালিকা फेচ করা হচ্ছে', state.categories);
        };

        // Navigation Switcher
        function switchTab(tabId) {
            document.querySelectorAll('.tab-page').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.nav-tab').forEach(el => {
                el.classList.remove('bg-slate-700', 'text-white', 'shadow-sm');
                el.classList.add('text-slate-300');
            });

            document.getElementById(`tab-${tabId}`).classList.remove('hidden');
            const activeNav = document.getElementById(`nav-${tabId}`);
            if (activeNav) {
                activeNav.classList.add('bg-slate-700', 'text-white', 'shadow-sm');
                activeNav.classList.remove('text-slate-300');
            }

            // Update Log Console for Context
            if(tabId === 'create-order') {
                showApiPayload('GET', '/api/v1/ondemand/parcels/categories', 'Endpoint #5: পার্সেল ক্যাটাগরি কনফিগারেশন', state.categories);
            } else if(tabId === 'active-orders') {
                showApiPayload('GET', '/api/v1/ondemand/parcels/active', 'Endpoints #9 & #10: একটিভ অর্ডারের লিস্ট ফেচিং', state.orders);
            } else if(tabId === 'payout-methods') {
                showApiPayload('GET', '/api/v1/payout-methods', 'Endpoints #13, #15: বিকাশ (bKash) পেআউট অ্যাকাউন্ট মেথডস', state.payoutMethods);
            }
        }

        // Render Categories (Endpoint 5 & 6)
        function renderCategories() {
            const container = document.getElementById('category-grid');
            container.innerHTML = state.categories.map(cat => `
                <button type="button" onclick="selectCategory('${cat.id}')" id="cat-btn-${cat.id}" class="cat-card p-3 rounded-xl border transition-all text-left flex flex-col justify-between ${state.selectedCategory === cat.id ? 'bg-red-500/10 border-red-500 text-white' : 'bg-slate-900/60 border-slate-700/60 text-slate-300 hover:border-slate-600'}">
                    <div class="flex items-center justify-between">
                        <i data-lucide="${cat.icon}" class="w-5 h-5 ${state.selectedCategory === cat.id ? 'text-red-400' : 'text-slate-400'}"></i>
                        ${state.selectedCategory === cat.id ? '<span class="w-2 h-2 rounded-full bg-red-400"></span>' : ''}
                    </div>
                    <div class="mt-2">
                        <div class="text-xs font-semibold">${cat.name}</div>
                        <div class="text-[10px] text-slate-400">${cat.desc}</div>
                    </div>
                </button>
            `).join('');
            lucide.createIcons();
        }

        function selectCategory(id) {
            state.selectedCategory = id;
            const catObj = state.categories.find(c => c.id === id);
            document.getElementById('selected-cat-badge').innerText = `Selected: ${catObj.name}`;
            renderCategories();
            showApiPayload('GET', `/api/v1/ondemand/parcels/categories/${id}`, `Endpoint #6: ${catObj.name} ক্যাটাগরির লিমিটেশন ও প্যারামিটারস`, {
                category_id: catObj.id,
                name: catObj.name,
                max_weight_kg: 10,
                max_value_bdt: 20000,
                allowed_payment_modes: ["COD", "PREPAID"]
            });
        }

        // Calculate COD Total
        function calculateCOD() {
            const price = parseFloat(document.getElementById('item_price').value) || 0;
            document.getElementById('display-total-cod').innerText = `৳${price.toFixed(2)}`;
        }

        // Create Order Submit (Endpoints 7 & 8)
        function handleCreateParcel(e) {
            e.preventDefault();
            const newOrder = {
                id: Math.random().toString(36).substring(2, 9).toUpperCase(),
                hash_id: Math.random().toString(36).substring(2, 14),
                status: "ASSIGNING_RIDER",
                item_name: document.getElementById('item_name').value,
                receiver: document.getElementById('receiver_name').value,
                phone: document.getElementById('receiver_phone').value,
                address: document.getElementById('receiver_address').value,
                amount: parseFloat(document.getElementById('item_price').value).toFixed(2),
                delivery_fee: "114.26",
                rider: {
                    name: "Finding nearest rider...",
                    phone: "N/A",
                    vehicle: "Motorbike",
                    rating: "N/A"
                },
                proof_images: []
            };

            state.orders.unshift(newOrder);
            renderOrders();
            showToast("পার্সেল অর্ডার সফলভাবে ক্রিয়েট হয়েছে!");

            // API Payload display
            showApiPayload('POST', '/api/v1/ondemand/parcels', 'Endpoints #7 & #8: পার্সেল অর্ডার সাবমিশন ও রেসপন্স', {
                request_body: {
                    pickup: {
                        name: document.getElementById('pickup_name').value,
                        phone: document.getElementById('pickup_phone').value,
                        address: document.getElementById('pickup_address').value,
                        lat: parseFloat(document.getElementById('pickup_lat').value),
                        lng: parseFloat(document.getElementById('pickup_lng').value)
                    },
                    item: {
                        category: state.selectedCategory,
                        name: newOrder.item_name,
                        price: newOrder.amount
                    },
                    receiver: {
                        name: newOrder.receiver,
                        phone: newOrder.phone,
                        address: newOrder.address
                    }
                },
                api_response: {
                    status: 201,
                    success: true,
                    data: newOrder
                }
            });

            switchTab('active-orders');
        }

        // Render Active Orders List (Endpoints 9, 10, 11, 12)
        function renderOrders() {
            document.getElementById('order-count-badge').innerText = state.orders.length;
            const container = document.getElementById('orders-list-container');
            
            if(state.orders.length === 0) {
                container.innerHTML = `<div class="bg-slate-800/40 p-8 rounded-2xl text-center text-slate-400">কোনো অর্ডার পাওয়া যায়নি।</div>`;
                return;
            }

            container.innerHTML = state.orders.map(order => {
                let statusBadgeClass = "bg-amber-500/20 text-amber-400 border-amber-500/30";
                if(order.status === 'DELIVERED') statusBadgeClass = "bg-emerald-500/20 text-emerald-400 border-emerald-500/30";
                if(order.status === 'CANCELLED') statusBadgeClass = "bg-red-500/20 text-red-400 border-red-500/30";
                if(order.status === 'PICKED') statusBadgeClass = "bg-blue-500/20 text-blue-400 border-blue-500/30";

                return `
                <div class="bg-slate-800/60 rounded-2xl p-5 border border-slate-700/80 shadow-lg space-y-4">
                    <!-- Top Info Header -->
                    <div class="flex flex-wrap items-center justify-between gap-2 border-b border-slate-700/60 pb-3">
                        <div class="flex items-center space-x-3">
                            <span class="text-sm font-bold text-white">#${order.id}</span>
                            <span class="text-xs font-mono text-slate-400 bg-slate-900 px-2 py-0.5 rounded border border-slate-700">Hash: ${order.hash_id}</span>
                        </div>
                        <div class="flex items-center space-x-2">
                            <span class="text-xs font-semibold px-2.5 py-1 rounded-full border ${statusBadgeClass}">${order.status}</span>
                            ${order.status !== 'CANCELLED' && order.status !== 'DELIVERED' ? `
                                <button onclick="openCancelModal('${order.id}')" class="text-xs bg-red-500/10 hover:bg-red-500/20 text-red-400 border border-red-500/20 px-2.5 py-1 rounded-lg transition-colors">
                                    অর্ডার ক্যানসেল
                                </button>
                            ` : ''}
                        </div>
                    </div>

                    <!-- Details Grid -->
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4 text-xs">
                        <div class="space-y-1">
                            <span class="text-slate-400">পার্সেল আইটেম</span>
                            <div class="text-slate-200 font-semibold text-sm">${order.item_name}</div>
                            <div class="text-emerald-400 font-mono font-bold">COD Value: ৳${order.amount}</div>
                        </div>
                        <div class="space-y-1">
                            <span class="text-slate-400">প্রাপকের বিবরণ</span>
                            <div class="text-slate-200 font-medium">${order.receiver} (${order.phone})</div>
                            <div class="text-slate-400 truncate">${order.address}</div>
                        </div>
                        <div class="bg-slate-900/60 p-3 rounded-xl border border-slate-700/50 space-y-1">
                            <div class="flex items-center justify-between text-slate-400">
                                <span class="flex items-center gap-1"><i data-lucide="bike" class="w-3.5 h-3.5 text-red-400"></i> রাইডার:</span>
                                <span class="text-slate-200 font-medium">${order.rider.name}</span>
                            </div>
                            <div class="text-[11px] text-slate-400">ফোন: <span class="text-slate-300 font-mono">${order.rider.phone}</span></div>
                            <div class="text-[11px] text-slate-400">যানবাহন: <span class="text-slate-300">${order.rider.vehicle}</span></div>
                        </div>
                    </div>

                    <!-- Proof Images / Action Footer -->
                    <div class="flex items-center justify-between pt-2">
                        <div class="flex items-center space-x-2">
                            ${(order.proof_images || []).map(img => `
                                <img src="${img}" alt="Proof" class="w-10 h-10 object-cover rounded-lg border border-slate-700">
                            `).join('')}
                        </div>
                        <button onclick="inspectOrderDetails('${order.id}')" class="text-xs text-indigo-400 hover:text-indigo-300 font-mono flex items-center gap-1">
                            <span>Inspect Payload (GET /parcels/${order.id})</span>
                            <i data-lucide="chevron-right" class="w-3.5 h-3.5"></i>
                        </button>
                    </div>
                </div>
            `}).join('');
            lucide.createIcons();
        }

        // Cancel Modal Actions (Endpoints 11 & 12)
        function openCancelModal(id) {
            document.getElementById('cancel_order_id').value = id;
            document.getElementById('cancel-modal').classList.remove('hidden');
        }

        function closeCancelModal() {
            document.getElementById('cancel-modal').classList.add('hidden');
        }

        function submitCancelOrder() {
            const id = document.getElementById('cancel_order_id').value;
            const reason = document.getElementById('cancel_reason').value;

            const order = state.orders.find(o => o.id === id);
            if(order) {
                order.status = "CANCELLED";
                renderOrders();
                showToast("অর্ডারটি বাতিল করা হয়েছে।");
                
                showApiPayload('POST', `/api/v1/ondemand/parcels/${id}/cancel`, 'Endpoints #11 & #12: ক্যানসেলেশন রিকোয়েস্ট execute করা হয়েছে', {
                    order_id: id,
                    reason: reason,
                    status: "CANCELLED",
                    cancelled_at: new Date().toISOString()
                });
            }
            closeCancelModal();
        }

        function inspectOrderDetails(id) {
            const order = state.orders.find(o => o.id === id);
            showApiPayload('GET', `/api/v1/ondemand/parcels/${id}`, `Endpoint #10: #${id} অর্ডারের পূর্ণাঙ্গ বিবরণ ফেচিং`, order);
        }

        // Payout Methods (Endpoints 13 - 20)
        function renderPayoutMethods() {
            const container = document.getElementById('payout-methods-container');
            container.innerHTML = state.payoutMethods.map(item => `
                <div class="bg-slate-900/80 p-5 rounded-xl border border-slate-700/70 space-y-3 relative">
                    <div class="flex items-center justify-between">
                        <div class="flex items-center space-x-3">
                            <div class="w-10 h-10 rounded-xl bg-pink-500/20 text-pink-400 flex items-center justify-center font-bold text-lg">b</div>
                            <div>
                                <div class="text-sm font-bold text-white flex items-center gap-2">
                                    <span>${item.gateway}</span>
                                    <span class="text-[10px] bg-slate-800 text-slate-300 px-2 py-0.5 rounded border border-slate-700">${item.account_type}</span>
                                </div>
                                <div class="text-xs font-mono text-slate-400 mt-0.5">${item.account_number}</div>
                            </div>
                        </div>
                        ${item.is_default ? `
                            <span class="bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 text-[10px] px-2.5 py-0.5 rounded-full font-bold">DEFAULT</span>
                        ` : ''}
                    </div>

                    <div class="flex items-center justify-end space-x-2 pt-2 border-t border-slate-800">
                        ${!item.is_default ? `
                            <button onclick="setDefaultPayout('${item.id}')" class="text-xs bg-slate-800 hover:bg-slate-700 text-slate-300 px-3 py-1 rounded-lg border border-slate-700 transition-colors">
                                Set Default (PATCH)
                            </button>
                        ` : ''}
                        <button onclick="deletePayoutMethod('${item.id}')" class="text-xs bg-red-500/10 hover:bg-red-500/20 text-red-400 px-3 py-1 rounded-lg border border-red-500/20 transition-colors">
                            Delete (DELETE)
                        </button>
                    </div>
                </div>
            `).join('');
            lucide.createIcons();
        }

        function openAddBkashModal() {
            document.getElementById('bkash-modal').classList.remove('hidden');
        }

        function closeAddBkashModal() {
            document.getElementById('bkash-modal').classList.add('hidden');
        }

        function handleAddBkash(e) {
            e.preventDefault();
            const type = document.getElementById('modal_bkash_type').value;
            const phone = document.getElementById('modal_bkash_phone').value;

            const newMethod = {
                id: `pm_bkash_${Math.floor(1000 + Math.random() * 9000)}`,
                gateway: "bKash",
                account_type: type,
                account_number: phone,
                is_default: state.payoutMethods.length === 0,
                created_at: new Date().toISOString().split('T')[0]
            };

            state.payoutMethods.push(newMethod);
            renderPayoutMethods();
            closeAddBkashModal();
            showToast("bKash মেথড যোগ করা হয়েছে!");

            showApiPayload('POST', '/api/v1/payout-methods', 'Endpoints #14 & #18: bKash অ্যাকাউন্ট অ্যাড করা হয়েছে', newMethod);
        }

        function setDefaultPayout(id) {
            state.payoutMethods.forEach(m => m.is_default = (m.id === id));
            renderPayoutMethods();
            showToast("ডিফল্ট বিকাশ মেথড আপডেট হয়েছে!");
            showApiPayload('PATCH', `/api/v1/payout-methods/${id}`, 'Endpoint #19: ডিফল্ট পেআউট অ্যাকাউন্ট আপডেট (PATCH)', {
                payout_method_id: id,
                is_default: true
            });
        }

        function deletePayoutMethod(id) {
            state.payoutMethods = state.payoutMethods.filter(m => m.id !== id);
            renderPayoutMethods();
            showToast("payout method deleted successfully");
            showApiPayload('DELETE', `/api/v1/payout-methods/${id}`, 'Endpoint #20: পেআউট মেথড ডিলিট (DELETE)', {
                message: "payout method deleted successfully",
                status: 200
            });
        }

        // Live API Payload Console Viewer Controller
        function showApiPayload(method, endpoint, desc, data) {
            const methodEl = document.getElementById('api-method');
            methodEl.innerText = method;

            // Colorize Method Badge
            methodEl.className = "px-2 py-0.5 text-xs font-bold font-mono rounded border " + 
                (method === 'GET' ? 'bg-blue-500/20 text-blue-400 border-blue-500/30' :
                 method === 'POST' ? 'bg-emerald-500/20 text-emerald-400 border-emerald-500/30' :
                 method === 'PATCH' ? 'bg-amber-500/20 text-amber-400 border-amber-500/30' :
                 'bg-red-500/20 text-red-400 border-red-500/30');

            document.getElementById('api-endpoint').innerText = endpoint;
            document.getElementById('api-desc').innerText = desc;
            document.getElementById('api-payload-code').innerText = JSON.stringify(data, null, 2);
        }

        function copyApiPayload() {
            const text = document.getElementById('api-payload-code').innerText;
            navigator.clipboard.writeText(text);
            showToast("পেলোড কোড কপি হয়েছে!");
        }

        function toggleApiConsole() {
            const panel = document.getElementById('api-console-panel');
            panel.classList.toggle('hidden');
        }

        // Toast Helper
        function showToast(msg) {
            const toast = document.getElementById('toast');
            document.getElementById('toast-msg').innerText = msg;
            toast.classList.remove('translate-y-20', 'opacity-0');
            setTimeout(() => {
                toast.classList.add('translate-y-20', 'opacity-0');
            }, 3000);
        }
    </script>
</body>
</html>
