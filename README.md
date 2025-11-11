<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>آلة حاسبة علمية شاملة</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/11.11.0/math.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&family=Roboto+Mono:wght@400;500&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        primary: '#5D5CDE',
                        'primary-dark': '#4845B8'
                    },
                    fontFamily: {
                        'arabic': ['Cairo', 'sans-serif'],
                        'mono': ['Roboto Mono', 'monospace']
                    }
                }
            }
        }
    </script>
    <style>
        * {
            font-family: 'Cairo', sans-serif;
        }

        .calculator-button {
            transition: all 0.2s ease;
        }

        .calculator-button:active {
            transform: scale(0.95);
        }

        .calculator-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(93, 92, 222, 0.3);
        }

        .result-display {
            font-family: 'Roboto Mono', monospace;
            direction: ltr;
            text-align: left;
        }

        input[type="number"], input[type="text"] {
            font-size: 16px !important;
        }

        .tab-button {
            transition: all 0.3s ease;
        }

        .tab-button.active {
            background: linear-gradient(135deg, #5D5CDE 0%, #4845B8 100%);
            color: white;
        }

        .modal-backdrop {
            backdrop-filter: blur(4px);
        }

        .slide-in {
            animation: slideIn 0.3s ease-out;
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateY(-20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .fade-in {
            animation: fadeIn 0.3s ease-in;
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        .history-item {
            transition: all 0.2s ease;
        }

        .history-item:hover {
            background-color: rgba(93, 92, 222, 0.1);
            transform: translateX(-5px);
        }

        .dark .history-item:hover {
            background-color: rgba(93, 92, 222, 0.2);
        }
    </style>
</head>
<body class="bg-white dark:bg-[#181818] text-gray-900 dark:text-white transition-colors duration-300 min-h-screen">
    <!-- Header -->
    <header class="bg-gradient-to-r from-primary to-primary-dark text-white py-4 px-4 shadow-lg">
        <div class="container mx-auto flex justify-between items-center">
            <h1 class="text-2xl md:text-3xl font-bold">🧮 آلة حاسبة علمية شاملة</h1>
            <div class="flex items-center gap-3">
                <select id="languageSelector" class="px-3 py-2 rounded-lg bg-white bg-opacity-20 text-white border border-white border-opacity-30 cursor-pointer hover:bg-opacity-30 transition text-base">
                    <option value="ar">العربية 🇸🇦</option>
                    <option value="en">English 🇬🇧</option>
                    <option value="fr">Français 🇫🇷</option>
                    <option value="es">Español 🇪🇸</option>
                    <option value="de">Deutsch 🇩🇪</option>
                </select>
                <button id="themeToggle" class="p-2 rounded-lg bg-white bg-opacity-20 hover:bg-opacity-30 transition">
                    <span class="text-2xl">🌙</span>
                </button>
            </div>
        </div>
    </header>

    <div class="container mx-auto px-4 py-6 max-w-7xl">
        <!-- Main Tabs -->
        <div class="flex flex-wrap gap-2 mb-6 bg-gray-100 dark:bg-gray-800 p-2 rounded-xl">
            <button class="tab-button active flex-1 min-w-[120px] px-4 py-3 rounded-lg font-semibold" data-tab="calculator">
                <span data-i18n="tab_calculator">آلة حاسبة</span>
            </button>
            <button class="tab-button flex-1 min-w-[120px] px-4 py-3 rounded-lg font-semibold" data-tab="geometry">
                <span data-i18n="tab_geometry">الأشكال الهندسية</span>
            </button>
            <button class="tab-button flex-1 min-w-[120px] px-4 py-3 rounded-lg font-semibold" data-tab="economics">
                <span data-i18n="tab_economics">الحسابات المالية</span>
            </button>
            <button class="tab-button flex-1 min-w-[120px] px-4 py-3 rounded-lg font-semibold" data-tab="conversions">
                <span data-i18n="tab_conversions">التحويلات</span>
            </button>
            <button class="tab-button flex-1 min-w-[120px] px-4 py-3 rounded-lg font-semibold" data-tab="history">
                <span data-i18n="tab_history">السجل</span>
            </button>
        </div>

        <!-- Calculator Tab -->
        <div id="calculatorTab" class="tab-content slide-in">
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <div class="lg:col-span-2">
                    <div class="bg-gradient-to-br from-gray-50 to-gray-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                        <!-- Display -->
                        <div class="mb-4">
                            <div class="bg-white dark:bg-gray-700 rounded-xl p-4 mb-2 shadow-inner">
                                <div class="text-gray-500 dark:text-gray-400 text-sm mb-1 result-display min-h-[24px]" id="calcExpression"></div>
                                <div class="text-3xl md:text-4xl font-bold result-display text-primary dark:text-primary min-h-[48px] break-all" id="calcDisplay">0</div>
                            </div>
                        </div>

                        <!-- Buttons -->
                        <div class="grid grid-cols-5 gap-2">
                            <!-- Row 1 -->
                            <button class="calculator-button bg-red-500 hover:bg-red-600 text-white px-4 py-4 rounded-xl font-semibold text-lg" onclick="clearCalculator()">C</button>
                            <button class="calculator-button bg-orange-500 hover:bg-orange-600 text-white px-4 py-4 rounded-xl font-semibold text-lg" onclick="deleteLast()">⌫</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendToCalc('(')">(</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendToCalc(')')">)</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl text-lg" onclick="appendToCalc('/')">÷</button>

                            <!-- Row 2 -->
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendFunction('sin(')">sin</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('7')">7</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('8')">8</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('9')">9</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl text-lg" onclick="appendToCalc('*')">×</button>

                            <!-- Row 3 -->
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendFunction('cos(')">cos</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('4')">4</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('5')">5</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('6')">6</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl text-lg" onclick="appendToCalc('-')">−</button>

                            <!-- Row 4 -->
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendFunction('tan(')">tan</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('1')">1</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('2')">2</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('3')">3</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl text-lg" onclick="appendToCalc('+')">+</button>

                            <!-- Row 5 -->
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendFunction('log(')">log</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('0')">0</button>
                            <button class="calculator-button bg-gray-200 dark:bg-gray-600 hover:bg-gray-300 dark:hover:bg-gray-500 px-4 py-4 rounded-xl font-semibold text-lg" onclick="appendToCalc('.')">.</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendToCalc('^')">^</button>
                            <button class="calculator-button bg-green-500 hover:bg-green-600 text-white px-4 py-4 rounded-xl text-lg font-semibold" onclick="calculate()">=</button>

                            <!-- Row 6 - Additional functions -->
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendFunction('sqrt(')">√</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendToCalc('pi')">π</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendToCalc('e')">e</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendFunction('ln(')">ln</button>
                            <button class="calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-4 rounded-xl" onclick="appendToCalc('!')">!</button>
                        </div>
                    </div>
                </div>

                <!-- Quick Functions Panel -->
                <div class="bg-gray-50 dark:bg-gray-800 rounded-2xl shadow-xl p-6">
                    <h3 class="text-xl font-bold mb-4" data-i18n="quick_functions">دوال سريعة</h3>
                    <div class="space-y-2">
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('asin(')">arcsin</button>
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('acos(')">arccos</button>
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('atan(')">arctan</button>
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('sinh(')">sinh</button>
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('cosh(')">cosh</button>
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('tanh(')">tanh</button>
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('abs(')">|x|</button>
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('ceil(')">⌈x⌉</button>
                        <button class="w-full calculator-button bg-primary hover:bg-primary-dark text-white px-4 py-3 rounded-xl text-right" onclick="appendFunction('floor(')">⌊x⌋</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Geometry Tab -->
        <div id="geometryTab" class="tab-content hidden">
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Circle -->
                <div class="bg-gradient-to-br from-blue-50 to-blue-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-blue-600 dark:text-blue-400">⭕ <span data-i18n="circle">الدائرة</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="radius">نصف القطر</label>
                            <input type="number" id="circleRadius" class="w-full px-4 py-2 rounded-lg border-2 border-blue-300 focus:border-blue-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateCircle()" class="w-full bg-blue-500 hover:bg-blue-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="circleResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>

                <!-- Rectangle -->
                <div class="bg-gradient-to-br from-green-50 to-green-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-green-600 dark:text-green-400">▭ <span data-i18n="rectangle">المستطيل</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="length">الطول</label>
                            <input type="number" id="rectLength" class="w-full px-4 py-2 rounded-lg border-2 border-green-300 focus:border-green-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="width">العرض</label>
                            <input type="number" id="rectWidth" class="w-full px-4 py-2 rounded-lg border-2 border-green-300 focus:border-green-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateRectangle()" class="w-full bg-green-500 hover:bg-green-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="rectangleResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>

                <!-- Triangle -->
                <div class="bg-gradient-to-br from-purple-50 to-purple-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-purple-600 dark:text-purple-400">△ <span data-i18n="triangle">المثلث</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="base">القاعدة</label>
                            <input type="number" id="triangleBase" class="w-full px-4 py-2 rounded-lg border-2 border-purple-300 focus:border-purple-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="height">الارتفاع</label>
                            <input type="number" id="triangleHeight" class="w-full px-4 py-2 rounded-lg border-2 border-purple-300 focus:border-purple-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateTriangle()" class="w-full bg-purple-500 hover:bg-purple-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="triangleResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>

                <!-- Sphere -->
                <div class="bg-gradient-to-br from-red-50 to-red-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-red-600 dark:text-red-400">⚫ <span data-i18n="sphere">الكرة</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="radius">نصف القطر</label>
                            <input type="number" id="sphereRadius" class="w-full px-4 py-2 rounded-lg border-2 border-red-300 focus:border-red-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateSphere()" class="w-full bg-red-500 hover:bg-red-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="sphereResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>

                <!-- Cylinder -->
                <div class="bg-gradient-to-br from-yellow-50 to-yellow-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-yellow-600 dark:text-yellow-400">🔲 <span data-i18n="cylinder">الأسطوانة</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="radius">نصف القطر</label>
                            <input type="number" id="cylinderRadius" class="w-full px-4 py-2 rounded-lg border-2 border-yellow-300 focus:border-yellow-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="height">الارتفاع</label>
                            <input type="number" id="cylinderHeight" class="w-full px-4 py-2 rounded-lg border-2 border-yellow-300 focus:border-yellow-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateCylinder()" class="w-full bg-yellow-500 hover:bg-yellow-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="cylinderResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>

                <!-- Cube -->
                <div class="bg-gradient-to-br from-indigo-50 to-indigo-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-indigo-600 dark:text-indigo-400">◻️ <span data-i18n="cube">المكعب</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="side_length">طول الضلع</label>
                            <input type="number" id="cubeSide" class="w-full px-4 py-2 rounded-lg border-2 border-indigo-300 focus:border-indigo-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateCube()" class="w-full bg-indigo-500 hover:bg-indigo-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="cubeResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Economics Tab -->
        <div id="economicsTab" class="tab-content hidden">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- Simple Interest -->
                <div class="bg-gradient-to-br from-emerald-50 to-emerald-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-emerald-600 dark:text-emerald-400">💰 <span data-i18n="simple_interest">الفائدة البسيطة</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="principal">المبلغ الأصلي</label>
                            <input type="number" id="siPrincipal" class="w-full px-4 py-2 rounded-lg border-2 border-emerald-300 focus:border-emerald-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="interest_rate">معدل الفائدة (%)</label>
                            <input type="number" id="siRate" class="w-full px-4 py-2 rounded-lg border-2 border-emerald-300 focus:border-emerald-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="time_years">المدة (سنوات)</label>
                            <input type="number" id="siTime" class="w-full px-4 py-2 rounded-lg border-2 border-emerald-300 focus:border-emerald-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateSimpleInterest()" class="w-full bg-emerald-500 hover:bg-emerald-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="simpleInterestResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>

                <!-- Compound Interest -->
                <div class="bg-gradient-to-br from-teal-50 to-teal-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-teal-600 dark:text-teal-400">📈 <span data-i18n="compound_interest">الفائدة المركبة</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="principal">المبلغ الأصلي</label>
                            <input type="number" id="ciPrincipal" class="w-full px-4 py-2 rounded-lg border-2 border-teal-300 focus:border-teal-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="interest_rate">معدل الفائدة (%)</label>
                            <input type="number" id="ciRate" class="w-full px-4 py-2 rounded-lg border-2 border-teal-300 focus:border-teal-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="time_years">المدة (سنوات)</label>
                            <input type="number" id="ciTime" class="w-full px-4 py-2 rounded-lg border-2 border-teal-300 focus:border-teal-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="compounds_per_year">عدد المركبات في السنة</label>
                            <input type="number" id="ciCompounds" class="w-full px-4 py-2 rounded-lg border-2 border-teal-300 focus:border-teal-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="12">
                        </div>
                        <button onclick="calculateCompoundInterest()" class="w-full bg-teal-500 hover:bg-teal-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="compoundInterestResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>

                <!-- Loan Calculator -->
                <div class="bg-gradient-to-br from-orange-50 to-orange-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-orange-600 dark:text-orange-400">🏦 <span data-i18n="loan_calculator">حاسبة القرض</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="loan_amount">مبلغ القرض</label>
                            <input type="number" id="loanAmount" class="w-full px-4 py-2 rounded-lg border-2 border-orange-300 focus:border-orange-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="interest_rate">معدل الفائدة السنوي (%)</label>
                            <input type="number" id="loanRate" class="w-full px-4 py-2 rounded-lg border-2 border-orange-300 focus:border-orange-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="loan_term">مدة القرض (شهور)</label>
                            <input type="number" id="loanTerm" class="w-full px-4 py-2 rounded-lg border-2 border-orange-300 focus:border-orange-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateLoan()" class="w-full bg-orange-500 hover:bg-orange-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="loanResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>

                <!-- ROI Calculator -->
                <div class="bg-gradient-to-br from-pink-50 to-pink-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-pink-600 dark:text-pink-400">📊 <span data-i18n="roi_calculator">حاسبة العائد على الاستثمار</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="initial_investment">الاستثمار الأولي</label>
                            <input type="number" id="roiInitial" class="w-full px-4 py-2 rounded-lg border-2 border-pink-300 focus:border-pink-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="final_value">القيمة النهائية</label>
                            <input type="number" id="roiFinal" class="w-full px-4 py-2 rounded-lg border-2 border-pink-300 focus:border-pink-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <button onclick="calculateROI()" class="w-full bg-pink-500 hover:bg-pink-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="calculate">احسب</span>
                        </button>
                        <div id="roiResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm space-y-1"></div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Conversions Tab -->
        <div id="conversionsTab" class="tab-content hidden">
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Length Conversion -->
                <div class="bg-gradient-to-br from-cyan-50 to-cyan-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-cyan-600 dark:text-cyan-400">📏 <span data-i18n="length_conversion">تحويل الطول</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="value">القيمة</label>
                            <input type="number" id="lengthValue" class="w-full px-4 py-2 rounded-lg border-2 border-cyan-300 focus:border-cyan-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="from">من</label>
                            <select id="lengthFrom" class="w-full px-4 py-2 rounded-lg border-2 border-cyan-300 focus:border-cyan-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="m">متر (m)</option>
                                <option value="km">كيلومتر (km)</option>
                                <option value="cm">سنتيمتر (cm)</option>
                                <option value="mm">ملليمتر (mm)</option>
                                <option value="mi">ميل (mi)</option>
                                <option value="yd">ياردة (yd)</option>
                                <option value="ft">قدم (ft)</option>
                                <option value="in">بوصة (in)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="to">إلى</label>
                            <select id="lengthTo" class="w-full px-4 py-2 rounded-lg border-2 border-cyan-300 focus:border-cyan-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="m">متر (m)</option>
                                <option value="km">كيلومتر (km)</option>
                                <option value="cm">سنتيمتر (cm)</option>
                                <option value="mm">ملليمتر (mm)</option>
                                <option value="mi">ميل (mi)</option>
                                <option value="yd">ياردة (yd)</option>
                                <option value="ft">قدم (ft)</option>
                                <option value="in">بوصة (in)</option>
                            </select>
                        </div>
                        <button onclick="convertLength()" class="w-full bg-cyan-500 hover:bg-cyan-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="convert">تحويل</span>
                        </button>
                        <div id="lengthResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm font-mono"></div>
                    </div>
                </div>

                <!-- Weight Conversion -->
                <div class="bg-gradient-to-br from-lime-50 to-lime-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-lime-600 dark:text-lime-400">⚖️ <span data-i18n="weight_conversion">تحويل الوزن</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="value">القيمة</label>
                            <input type="number" id="weightValue" class="w-full px-4 py-2 rounded-lg border-2 border-lime-300 focus:border-lime-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="from">من</label>
                            <select id="weightFrom" class="w-full px-4 py-2 rounded-lg border-2 border-lime-300 focus:border-lime-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="kg">كيلوغرام (kg)</option>
                                <option value="g">غرام (g)</option>
                                <option value="mg">ملليغرام (mg)</option>
                                <option value="lb">رطل (lb)</option>
                                <option value="oz">أونصة (oz)</option>
                                <option value="ton">طن (ton)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="to">إلى</label>
                            <select id="weightTo" class="w-full px-4 py-2 rounded-lg border-2 border-lime-300 focus:border-lime-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="kg">كيلوغرام (kg)</option>
                                <option value="g">غرام (g)</option>
                                <option value="mg">ملليغرام (mg)</option>
                                <option value="lb">رطل (lb)</option>
                                <option value="oz">أونصة (oz)</option>
                                <option value="ton">طن (ton)</option>
                            </select>
                        </div>
                        <button onclick="convertWeight()" class="w-full bg-lime-500 hover:bg-lime-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="convert">تحويل</span>
                        </button>
                        <div id="weightResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm font-mono"></div>
                    </div>
                </div>

                <!-- Temperature Conversion -->
                <div class="bg-gradient-to-br from-rose-50 to-rose-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-rose-600 dark:text-rose-400">🌡️ <span data-i18n="temperature_conversion">تحويل درجة الحرارة</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="value">القيمة</label>
                            <input type="number" id="tempValue" class="w-full px-4 py-2 rounded-lg border-2 border-rose-300 focus:border-rose-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="from">من</label>
                            <select id="tempFrom" class="w-full px-4 py-2 rounded-lg border-2 border-rose-300 focus:border-rose-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="C">سيليزيوس (°C)</option>
                                <option value="F">فهرنهايت (°F)</option>
                                <option value="K">كلفن (K)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="to">إلى</label>
                            <select id="tempTo" class="w-full px-4 py-2 rounded-lg border-2 border-rose-300 focus:border-rose-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="C">سيليزيوس (°C)</option>
                                <option value="F">فهرنهايت (°F)</option>
                                <option value="K">كلفن (K)</option>
                            </select>
                        </div>
                        <button onclick="convertTemperature()" class="w-full bg-rose-500 hover:bg-rose-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="convert">تحويل</span>
                        </button>
                        <div id="tempResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm font-mono"></div>
                    </div>
                </div>

                <!-- Currency Conversion -->
                <div class="bg-gradient-to-br from-amber-50 to-amber-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-amber-600 dark:text-amber-400">💱 <span data-i18n="currency_conversion">تحويل العملات</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="amount">المبلغ</label>
                            <input type="number" id="currencyValue" class="w-full px-4 py-2 rounded-lg border-2 border-amber-300 focus:border-amber-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="from">من</label>
                            <select id="currencyFrom" class="w-full px-4 py-2 rounded-lg border-2 border-amber-300 focus:border-amber-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="USD">دولار أمريكي (USD)</option>
                                <option value="EUR">يورو (EUR)</option>
                                <option value="GBP">جنيه استرليني (GBP)</option>
                                <option value="SAR">ريال سعودي (SAR)</option>
                                <option value="AED">درهم إماراتي (AED)</option>
                                <option value="EGP">جنيه مصري (EGP)</option>
                                <option value="JPY">ين ياباني (JPY)</option>
                                <option value="CNY">يوان صيني (CNY)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="to">إلى</label>
                            <select id="currencyTo" class="w-full px-4 py-2 rounded-lg border-2 border-amber-300 focus:border-amber-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="USD">دولار أمريكي (USD)</option>
                                <option value="EUR">يورو (EUR)</option>
                                <option value="GBP">جنيه استرليني (GBP)</option>
                                <option value="SAR">ريال سعودي (SAR)</option>
                                <option value="AED">درهم إماراتي (AED)</option>
                                <option value="EGP">جنيه مصري (EGP)</option>
                                <option value="JPY">ين ياباني (JPY)</option>
                                <option value="CNY">يوان صيني (CNY)</option>
                            </select>
                        </div>
                        <button onclick="convertCurrency()" class="w-full bg-amber-500 hover:bg-amber-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="convert">تحويل</span>
                        </button>
                        <div id="currencyResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm font-mono"></div>
                        <p class="text-xs text-gray-500 dark:text-gray-400" data-i18n="currency_note">ملاحظة: الأسعار تقريبية وقد تختلف عن الأسعار الفعلية</p>
                    </div>
                </div>

                <!-- Volume Conversion -->
                <div class="bg-gradient-to-br from-sky-50 to-sky-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-sky-600 dark:text-sky-400">🧊 <span data-i18n="volume_conversion">تحويل الحجم</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="value">القيمة</label>
                            <input type="number" id="volumeValue" class="w-full px-4 py-2 rounded-lg border-2 border-sky-300 focus:border-sky-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="from">من</label>
                            <select id="volumeFrom" class="w-full px-4 py-2 rounded-lg border-2 border-sky-300 focus:border-sky-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="l">لتر (l)</option>
                                <option value="ml">ملليلتر (ml)</option>
                                <option value="m3">متر مكعب (m³)</option>
                                <option value="gal">غالون (gal)</option>
                                <option value="qt">كوارت (qt)</option>
                                <option value="pt">باينت (pt)</option>
                                <option value="cup">كوب (cup)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="to">إلى</label>
                            <select id="volumeTo" class="w-full px-4 py-2 rounded-lg border-2 border-sky-300 focus:border-sky-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="l">لتر (l)</option>
                                <option value="ml">ملليلتر (ml)</option>
                                <option value="m3">متر مكعب (m³)</option>
                                <option value="gal">غالون (gal)</option>
                                <option value="qt">كوارت (qt)</option>
                                <option value="pt">باينت (pt)</option>
                                <option value="cup">كوب (cup)</option>
                            </select>
                        </div>
                        <button onclick="convertVolume()" class="w-full bg-sky-500 hover:bg-sky-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="convert">تحويل</span>
                        </button>
                        <div id="volumeResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm font-mono"></div>
                    </div>
                </div>

                <!-- Area Conversion -->
                <div class="bg-gradient-to-br from-violet-50 to-violet-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                    <h3 class="text-2xl font-bold mb-4 text-violet-600 dark:text-violet-400">📐 <span data-i18n="area_conversion">تحويل المساحة</span></h3>
                    <div class="space-y-3">
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="value">القيمة</label>
                            <input type="number" id="areaValue" class="w-full px-4 py-2 rounded-lg border-2 border-violet-300 focus:border-violet-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600" placeholder="0">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="from">من</label>
                            <select id="areaFrom" class="w-full px-4 py-2 rounded-lg border-2 border-violet-300 focus:border-violet-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="m2">متر مربع (m²)</option>
                                <option value="km2">كيلومتر مربع (km²)</option>
                                <option value="cm2">سنتيمتر مربع (cm²)</option>
                                <option value="ha">هكتار (ha)</option>
                                <option value="ac">فدان (acre)</option>
                                <option value="ft2">قدم مربع (ft²)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-semibold mb-1" data-i18n="to">إلى</label>
                            <select id="areaTo" class="w-full px-4 py-2 rounded-lg border-2 border-violet-300 focus:border-violet-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600">
                                <option value="m2">متر مربع (m²)</option>
                                <option value="km2">كيلومتر مربع (km²)</option>
                                <option value="cm2">سنتيمتر مربع (cm²)</option>
                                <option value="ha">هكتار (ha)</option>
                                <option value="ac">فدان (acre)</option>
                                <option value="ft2">قدم مربع (ft²)</option>
                            </select>
                        </div>
                        <button onclick="convertArea()" class="w-full bg-violet-500 hover:bg-violet-600 text-white px-4 py-3 rounded-xl font-semibold transition">
                            <span data-i18n="convert">تحويل</span>
                        </button>
                        <div id="areaResult" class="bg-white dark:bg-gray-700 p-4 rounded-lg text-sm font-mono"></div>
                    </div>
                </div>
            </div>
        </div>

        <!-- History Tab -->
        <div id="historyTab" class="tab-content hidden">
            <div class="bg-gradient-to-br from-gray-50 to-gray-100 dark:from-gray-800 dark:to-gray-900 rounded-2xl shadow-xl p-6">
                <div class="flex justify-between items-center mb-6">
                    <h3 class="text-2xl font-bold">📜 <span data-i18n="calculation_history">سجل العمليات الحسابية</span></h3>
                    <button onclick="clearHistory()" class="bg-red-500 hover:bg-red-600 text-white px-4 py-2 rounded-xl font-semibold transition">
                        <span data-i18n="clear_history">مسح السجل</span>
                    </button>
                </div>
                <div id="historyList" class="space-y-2 max-h-[600px] overflow-y-auto">
                    <p class="text-gray-500 dark:text-gray-400 text-center py-8" data-i18n="no_history">لا توجد عمليات حسابية في السجل</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Footer -->
    <footer class="bg-gray-100 dark:bg-gray-900 mt-12 py-6 px-4">
        <div class="container mx-auto text-center text-gray-600 dark:text-gray-400">
            <p data-i18n="footer_text">آلة حاسبة علمية شاملة - جميع الحقوق محفوظة © 2024</p>
        </div>
    </footer>

    <script>
        // Translations
        const translations = {
            ar: {
                tab_calculator: "آلة حاسبة",
                tab_geometry: "الأشكال الهندسية",
                tab_economics: "الحسابات المالية",
                tab_conversions: "التحويلات",
                tab_history: "السجل",
                quick_functions: "دوال سريعة",
                circle: "الدائرة",
                rectangle: "المستطيل",
                triangle: "المثلث",
                sphere: "الكرة",
                cylinder: "الأسطوانة",
                cube: "المكعب",
                radius: "نصف القطر",
                length: "الطول",
                width: "العرض",
                base: "القاعدة",
                height: "الارتفاع",
                side_length: "طول الضلع",
                calculate: "احسب",
                simple_interest: "الفائدة البسيطة",
                compound_interest: "الفائدة المركبة",
                loan_calculator: "حاسبة القرض",
                roi_calculator: "حاسبة العائد على الاستثمار",
                principal: "المبلغ الأصلي",
                interest_rate: "معدل الفائدة (%)",
                time_years: "المدة (سنوات)",
                compounds_per_year: "عدد المركبات في السنة",
                loan_amount: "مبلغ القرض",
                loan_term: "مدة القرض (شهور)",
                initial_investment: "الاستثمار الأولي",
                final_value: "القيمة النهائية",
                length_conversion: "تحويل الطول",
                weight_conversion: "تحويل الوزن",
                temperature_conversion: "تحويل درجة الحرارة",
                currency_conversion: "تحويل العملات",
                volume_conversion: "تحويل الحجم",
                area_conversion: "تحويل المساحة",
                value: "القيمة",
                amount: "المبلغ",
                from: "من",
                to: "إلى",
                convert: "تحويل",
                currency_note: "ملاحظة: الأسعار تقريبية وقد تختلف عن الأسعار الفعلية",
                calculation_history: "سجل العمليات الحسابية",
                clear_history: "مسح السجل",
                no_history: "لا توجد عمليات حسابية في السجل",
                footer_text: "آلة حاسبة علمية شاملة - جميع الحقوق محفوظة © 2024",
                area: "المساحة",
                perimeter: "المحيط",
                circumference: "المحيط",
                volume: "الحجم",
                surface_area: "مساحة السطح",
                interest: "الفائدة",
                total_amount: "المبلغ الإجمالي",
                monthly_payment: "الدفعة الشهرية",
                total_interest: "إجمالي الفائدة",
                total_payment: "إجمالي المدفوعات",
                roi_percentage: "العائد على الاستثمار (%)",
                profit_loss: "الربح/الخسارة",
                result: "النتيجة"
            },
            en: {
                tab_calculator: "Calculator",
                tab_geometry: "Geometry",
                tab_economics: "Finance",
                tab_conversions: "Conversions",
                tab_history: "History",
                quick_functions: "Quick Functions",
                circle: "Circle",
                rectangle: "Rectangle",
                triangle: "Triangle",
                sphere: "Sphere",
                cylinder: "Cylinder",
                cube: "Cube",
                radius: "Radius",
                length: "Length",
                width: "Width",
                base: "Base",
                height: "Height",
                side_length: "Side Length",
                calculate: "Calculate",
                simple_interest: "Simple Interest",
                compound_interest: "Compound Interest",
                loan_calculator: "Loan Calculator",
                roi_calculator: "ROI Calculator",
                principal: "Principal",
                interest_rate: "Interest Rate (%)",
                time_years: "Time (Years)",
                compounds_per_year: "Compounds Per Year",
                loan_amount: "Loan Amount",
                loan_term: "Loan Term (Months)",
                initial_investment: "Initial Investment",
                final_value: "Final Value",
                length_conversion: "Length Conversion",
                weight_conversion: "Weight Conversion",
                temperature_conversion: "Temperature Conversion",
                currency_conversion: "Currency Conversion",
                volume_conversion: "Volume Conversion",
                area_conversion: "Area Conversion",
                value: "Value",
                amount: "Amount",
                from: "From",
                to: "To",
                convert: "Convert",
                currency_note: "Note: Rates are approximate and may differ from actual rates",
                calculation_history: "Calculation History",
                clear_history: "Clear History",
                no_history: "No calculations in history",
                footer_text: "Comprehensive Scientific Calculator - All Rights Reserved © 2024",
                area: "Area",
                perimeter: "Perimeter",
                circumference: "Circumference",
                volume: "Volume",
                surface_area: "Surface Area",
                interest: "Interest",
                total_amount: "Total Amount",
                monthly_payment: "Monthly Payment",
                total_interest: "Total Interest",
                total_payment: "Total Payment",
                roi_percentage: "ROI (%)",
                profit_loss: "Profit/Loss",
                result: "Result"
            },
            fr: {
                tab_calculator: "Calculatrice",
                tab_geometry: "Géométrie",
                tab_economics: "Finance",
                tab_conversions: "Conversions",
                tab_history: "Historique",
                quick_functions: "Fonctions Rapides",
                circle: "Cercle",
                rectangle: "Rectangle",
                triangle: "Triangle",
                sphere: "Sphère",
                cylinder: "Cylindre",
                cube: "Cube",
                radius: "Rayon",
                length: "Longueur",
                width: "Largeur",
                base: "Base",
                height: "Hauteur",
                side_length: "Longueur du Côté",
                calculate: "Calculer",
                simple_interest: "Intérêt Simple",
                compound_interest: "Intérêt Composé",
                loan_calculator: "Calculateur de Prêt",
                roi_calculator: "Calculateur de ROI",
                principal: "Principal",
                interest_rate: "Taux d'Intérêt (%)",
                time_years: "Durée (Années)",
                compounds_per_year: "Compositions par An",
                loan_amount: "Montant du Prêt",
                loan_term: "Durée du Prêt (Mois)",
                initial_investment: "Investissement Initial",
                final_value: "Valeur Finale",
                length_conversion: "Conversion de Longueur",
                weight_conversion: "Conversion de Poids",
                temperature_conversion: "Conversion de Température",
                currency_conversion: "Conversion de Devises",
                volume_conversion: "Conversion de Volume",
                area_conversion: "Conversion de Surface",
                value: "Valeur",
                amount: "Montant",
                from: "De",
                to: "À",
                convert: "Convertir",
                currency_note: "Note: Les taux sont approximatifs",
                calculation_history: "Historique des Calculs",
                clear_history: "Effacer l'Historique",
                no_history: "Aucun calcul dans l'historique",
                footer_text: "Calculatrice Scientifique Complète - Tous Droits Réservés © 2024",
                area: "Surface",
                perimeter: "Périmètre",
                circumference: "Circonférence",
                volume: "Volume",
                surface_area: "Surface",
                interest: "Intérêt",
                total_amount: "Montant Total",
                monthly_payment: "Paiement Mensuel",
                total_interest: "Intérêt Total",
                total_payment: "Paiement Total",
                roi_percentage: "ROI (%)",
                profit_loss: "Profit/Perte",
                result: "Résultat"
            },
            es: {
                tab_calculator: "Calculadora",
                tab_geometry: "Geometría",
                tab_economics: "Finanzas",
                tab_conversions: "Conversiones",
                tab_history: "Historial",
                quick_functions: "Funciones Rápidas",
                circle: "Círculo",
                rectangle: "Rectángulo",
                triangle: "Triángulo",
                sphere: "Esfera",
                cylinder: "Cilindro",
                cube: "Cubo",
                radius: "Radio",
                length: "Longitud",
                width: "Ancho",
                base: "Base",
                height: "Altura",
                side_length: "Longitud del Lado",
                calculate: "Calcular",
                simple_interest: "Interés Simple",
                compound_interest: "Interés Compuesto",
                loan_calculator: "Calculadora de Préstamos",
                roi_calculator: "Calculadora de ROI",
                principal: "Principal",
                interest_rate: "Tasa de Interés (%)",
                time_years: "Tiempo (Años)",
                compounds_per_year: "Composiciones por Año",
                loan_amount: "Monto del Préstamo",
                loan_term: "Plazo del Préstamo (Meses)",
                initial_investment: "Inversión Inicial",
                final_value: "Valor Final",
                length_conversion: "Conversión de Longitud",
                weight_conversion: "Conversión de Peso",
                temperature_conversion: "Conversión de Temperatura",
                currency_conversion: "Conversión de Monedas",
                volume_conversion: "Conversión de Volumen",
                area_conversion: "Conversión de Área",
                value: "Valor",
                amount: "Cantidad",
                from: "De",
                to: "A",
                convert: "Convertir",
                currency_note: "Nota: Las tasas son aproximadas",
                calculation_history: "Historial de Cálculos",
                clear_history: "Borrar Historial",
                no_history: "No hay cálculos en el historial",
                footer_text: "Calculadora Científica Completa - Todos los Derechos Reservados © 2024",
                area: "Área",
                perimeter: "Perímetro",
                circumference: "Circunferencia",
                volume: "Volumen",
                surface_area: "Área de Superficie",
                interest: "Interés",
                total_amount: "Monto Total",
                monthly_payment: "Pago Mensual",
                total_interest: "Interés Total",
                total_payment: "Pago Total",
                roi_percentage: "ROI (%)",
                profit_loss: "Ganancia/Pérdida",
                result: "Resultado"
            },
            de: {
                tab_calculator: "Rechner",
                tab_geometry: "Geometrie",
                tab_economics: "Finanzen",
                tab_conversions: "Umrechnungen",
                tab_history: "Verlauf",
                quick_functions: "Schnellfunktionen",
                circle: "Kreis",
                rectangle: "Rechteck",
                triangle: "Dreieck",
                sphere: "Kugel",
                cylinder: "Zylinder",
                cube: "Würfel",
                radius: "Radius",
                length: "Länge",
                width: "Breite",
                base: "Basis",
                height: "Höhe",
                side_length: "Seitenlänge",
                calculate: "Berechnen",
                simple_interest: "Einfacher Zins",
                compound_interest: "Zinseszins",
                loan_calculator: "Darlehensrechner",
                roi_calculator: "ROI-Rechner",
                principal: "Kapital",
                interest_rate: "Zinssatz (%)",
                time_years: "Zeit (Jahre)",
                compounds_per_year: "Zinsperioden pro Jahr",
                loan_amount: "Darlehensbetrag",
                loan_term: "Darlehenslaufzeit (Monate)",
                initial_investment: "Anfangsinvestition",
                final_value: "Endwert",
                length_conversion: "Längenumrechnung",
                weight_conversion: "Gewichtsumrechnung",
                temperature_conversion: "Temperaturumrechnung",
                currency_conversion: "Währungsumrechnung",
                volume_conversion: "Volumenumrechnung",
                area_conversion: "Flächenumrechnung",
                value: "Wert",
                amount: "Betrag",
                from: "Von",
                to: "Zu",
                convert: "Umrechnen",
                currency_note: "Hinweis: Die Kurse sind ungefähr",
                calculation_history: "Berechnungsverlauf",
                clear_history: "Verlauf löschen",
                no_history: "Keine Berechnungen im Verlauf",
                footer_text: "Umfassender Wissenschaftlicher Rechner - Alle Rechte Vorbehalten © 2024",
                area: "Fläche",
                perimeter: "Umfang",
                circumference: "Umfang",
                volume: "Volumen",
                surface_area: "Oberfläche",
                interest: "Zinsen",
                total_amount: "Gesamtbetrag",
                monthly_payment: "Monatliche Zahlung",
                total_interest: "Gesamtzinsen",
                total_payment: "Gesamtzahlung",
                roi_percentage: "ROI (%)",
                profit_loss: "Gewinn/Verlust",
                result: "Ergebnis"
            }
        };

        let currentLang = 'ar';
        let calcExpression = '';
        let history = [];

        // Dark mode
        if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
            document.documentElement.classList.add('dark');
            document.getElementById('themeToggle').innerHTML = '<span class="text-2xl">☀️</span>';
        }

        window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', event => {
            if (event.matches) {
                document.documentElement.classList.add('dark');
                document.getElementById('themeToggle').innerHTML = '<span class="text-2xl">☀️</span>';
            } else {
                document.documentElement.classList.remove('dark');
                document.getElementById('themeToggle').innerHTML = '<span class="text-2xl">🌙</span>';
            }
        });

        document.getElementById('themeToggle').addEventListener('click', () => {
            document.documentElement.classList.toggle('dark');
            const isDark = document.documentElement.classList.contains('dark'
