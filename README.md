<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مراقب البيئة والنباتات</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Tajawal', sans-serif;
            direction: rtl;
        }
    </style>
</head>
<body class="bg-gray-100 p-4 min-h-screen flex items-center justify-center">
    <div class="container mx-auto max-w-lg bg-white p-6 rounded-3xl shadow-lg border border-gray-200">
        <h1 class="text-3xl font-bold text-center text-green-700 mb-6">مراقب البيئة والنباتات</h1>

        <!-- قسم الطقس -->
        <div class="mb-6 bg-green-50 p-4 rounded-xl border border-green-200">
            <h2 class="text-xl font-semibold mb-3 text-green-600">الطقس وجودة الهواء</h2>
            <div class="mb-4">
                <input id="city-input" type="text" placeholder="أدخل اسم المدينة" class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500">
            </div>
            <div class="flex space-x-2 space-x-reverse justify-end">
                <button id="get-location-btn" class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-full transition duration-300 transform hover:scale-105 shadow-md">
                    جلب الموقع الحالي
                </button>
                <button id="get-weather-btn" class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-full transition duration-300 transform hover:scale-105 shadow-md">
                    احصل على الطقس
                </button>
            </div>
            <div id="weather-display" class="mt-4 text-center text-gray-700"></div>
        </div>

        <!-- قسم الذكاء الاصطناعي -->
        <div class="mb-6 bg-gray-50 p-4 rounded-xl border border-gray-200">
            <h2 class="text-xl font-semibold mb-3 text-gray-600">تحليل أمراض النباتات</h2>
            <button id="analyze-ai-btn" class="w-full bg-purple-500 hover:bg-purple-600 text-white font-bold py-3 px-4 rounded-full transition duration-300 transform hover:scale-105 shadow-md">
                تحليل أمراض النباتات بالذكاء الاصطناعي
            </button>
            <div id="ai-result" class="mt-4 text-gray-700 leading-relaxed"></div>
            <button id="play-audio-btn" class="hidden w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 mt-4 rounded-full transition duration-300 transform hover:scale-105 shadow-md">
                تشغيل التحليل الصوتي
            </button>
        </div>

        <!-- قسم تحديد النبات -->
        <div class="mb-6 bg-gray-50 p-4 rounded-xl border border-gray-200">
            <h2 class="text-xl font-semibold mb-3 text-gray-600">تحديد النبات</h2>
            <input type="file" id="plant-image-input" accept="image/*" class="w-full text-sm text-gray-500
                file:mr-4 file:py-2 file:px-4
                file:rounded-full file:border-0
                file:text-sm file:font-semibold
                file:bg-green-50 file:text-green-700
                hover:file:bg-green-100
                mb-4
            "/>
            <button id="identify-plant-btn" class="w-full bg-orange-500 hover:bg-orange-600 text-white font-bold py-3 px-4 rounded-full transition duration-300 transform hover:scale-105 shadow-md">
                تحديد النبات
            </button>
            <div id="plant-result" class="mt-4 text-gray-700 leading-relaxed"></div>
        </div>

        <!-- قسم البحث عن نبات بالاسم -->
        <div class="mb-6 bg-gray-50 p-4 rounded-xl border border-gray-200">
            <h2 class="text-xl font-semibold mb-3 text-gray-600">البحث عن نبات بالاسم</h2>
            <input id="plant-name-input" type="text" placeholder="أدخل اسم النبات" class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-gray-500 mb-4">
            <button id="search-plant-btn" class="w-full bg-sky-500 hover:bg-sky-600 text-white font-bold py-3 px-4 rounded-full transition duration-300 transform hover:scale-105 shadow-md">
                ابحث عن النبات
            </button>
            <div id="plant-search-result" class="mt-4 text-gray-700 leading-relaxed"></div>
        </div>

        <!-- رسالة الخطأ والتحميل -->
        <div id="loading-message" class="hidden text-center text-blue-500 mt-4">
            جارٍ التحميل، يرجى الانتظار...
        </div>
        <div id="error-message" class="hidden text-center text-red-500 mt-4"></div>

    </div>

    <script type="module">
        // Global variables for Firebase
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // API Keys (These are placeholders, the real API keys will be provided by the runtime environment)
        const WEATHER_API_KEY = '';
        const GEMINI_API_KEY = '';

        // UI Elements
        const cityInput = document.getElementById('city-input');
        const getLocationBtn = document.getElementById('get-location-btn');
        const getWeatherBtn = document.getElementById('get-weather-btn');
        const weatherDisplay = document.getElementById('weather-display');
        const analyzeAiBtn = document.getElementById('analyze-ai-btn');
        const aiResultDiv = document.getElementById('ai-result');
        const playAudioBtn = document.getElementById('play-audio-btn');
        const plantImageInput = document.getElementById('plant-image-input');
        const identifyPlantBtn = document.getElementById('identify-plant-btn');
        const plantResultDiv = document.getElementById('plant-result');
        const plantNameInput = document.getElementById('plant-name-input');
        const searchPlantBtn = document.getElementById('search-plant-btn');
        const plantSearchResultDiv = document.getElementById('plant-search-result');
        const loadingMessage = document.getElementById('loading-message');
        const errorMessage = document.getElementById('error-message');

        // State
        let weatherData = null;
        let lastAiAnalysis = null;
        let audioContext = null;

        // Firebase Imports and Initialization
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, doc, setDoc, addDoc, getDoc, getDocs, query, where, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { setLogLevel } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        setLogLevel('debug');

        let userId = null;

        onAuthStateChanged(auth, async (user) => {
            if (user) {
                userId = user.uid;
                console.log("Firebase Auth State Changed. User ID:", userId);
            } else {
                console.log("No user signed in. Signing in anonymously...");
                await signInAnonymously(auth);
            }
        });

        if (initialAuthToken) {
            signInWithCustomToken(auth, initialAuthToken)
                .then(() => {
                    console.log("Signed in with custom token.");
                })
                .catch((error) => {
                    console.error("Error signing in with custom token:", error);
                    signInAnonymously(auth);
                });
        }

        function showLoading(show) {
            loadingMessage.classList.toggle('hidden', !show);
            errorMessage.classList.add('hidden');
        }

        function showError(message) {
            errorMessage.textContent = message;
            errorMessage.classList.remove('hidden');
            loadingMessage.classList.add('hidden');
        }

        // --- Weather & Location Functions ---
        getLocationBtn.addEventListener('click', () => {
            if (navigator.geolocation) {
                showLoading(true);
                navigator.geolocation.getCurrentPosition(position => {
                    const lat = position.coords.latitude;
                    const lon = position.coords.longitude;
                    getWeatherByCoords(lat, lon);
                }, error => {
                    showError("تعذر الحصول على الموقع. يرجى إدخال اسم المدينة يدوياً.");
                    showLoading(false);
                });
            } else {
                showError("التحديد الجغرافي غير مدعوم في متصفحك.");
            }
        });

        getWeatherBtn.addEventListener('click', () => {
            const city = cityInput.value.trim();
            if (city) {
                getWeatherByCity(city);
            } else {
                showError("يرجى إدخال اسم المدينة.");
            }
        });

        async function getWeatherByCity(city) {
            showLoading(true);
            try {
                const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=${WEATHER_API_KEY}&lang=ar`);
                if (!response.ok) throw new Error('المدينة غير موجودة أو هناك خطأ في الاتصال.');
                const data = await response.json();
                displayWeather(data);
                weatherData = data;
            } catch (error) {
                showError(error.message);
            } finally {
                showLoading(false);
            }
        }

        async function getWeatherByCoords(lat, lon) {
            showLoading(true);
            try {
                const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${WEATHER_API_KEY}&lang=ar`);
                if (!response.ok) throw new Error('حدث خطأ أثناء جلب بيانات الطقس.');
                const data = await response.json();
                displayWeather(data);
                weatherData = data;
            } catch (error) {
                showError(error.message);
            } finally {
                showLoading(false);
            }
        }

        function displayWeather(data) {
            const { main, weather, wind, name } = data;
            const weatherHtml = `
                <div class="p-4 bg-green-100 rounded-lg">
                    <h3 class="text-xl font-bold mb-2">${name}</h3>
                    <p><strong>درجة الحرارة:</strong> ${main.temp}°C</p>
                    <p><strong>الحالة:</strong> ${weather[0].description}</p>
                    <p><strong>الرطوبة:</strong> ${main.humidity}%</p>
                    <p><strong>سرعة الرياح:</strong> ${wind.speed} م/ث</p>
                </div>
            `;
            weatherDisplay.innerHTML = weatherHtml;
        }

        // --- AI Analysis Functions ---
        analyzeAiBtn.addEventListener('click', async () => {
            if (!weatherData) {
                showError("يرجى جلب بيانات الطقس أولاً.");
                return;
            }
            showLoading(true);
            try {
                const prompt = `استناداً إلى بيانات الطقس التالية:
                - درجة الحرارة: ${weatherData.main.temp}°C
                - الرطوبة: ${weatherData.main.humidity}%
                - سرعة الرياح: ${weatherData.wind.speed} م/ث
                - وصف الطقس: ${weatherData.weather[0].description}

                حلل هذه البيانات وقدم تقريراً مفصلاً عن الأمراض المحتملة التي قد تصيب النباتات في هذه البيئة. قدم نصائح للوقاية منها. اذكر أيضاً الأمراض الشائعة التي قد تصيب النباتات حسب هذا الموسم (الصيف، الشتاء، إلخ).`;

                const payload = {
                    contents: [{ parts: [{ text: prompt }] }],
                    generationConfig: {
                        temperature: 0.7,
                        topP: 0.9,
                        topK: 40,
                    },
                };
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${GEMINI_API_KEY}`;
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (!response.ok) throw new Error('فشل في الاتصال بنموذج الذكاء الاصطناعي.');
                const result = await response.json();
                const text = result?.candidates?.[0]?.content?.parts?.[0]?.text || 'لم يتم الحصول على تحليل مناسب.';
                lastAiAnalysis = text;
                aiResultDiv.innerHTML = `<div class="p-4 bg-purple-100 rounded-lg">${text.replace(/\n/g, '<br><br>')}</div>`;
                playAudioBtn.classList.remove('hidden');
            } catch (error) {
                showError(error.message);
                console.error(error);
            } finally {
                showLoading(false);
            }
        });

        // --- Plant Identification Functions ---
        plantImageInput.addEventListener('change', () => {
            if (plantImageInput.files.length > 0) {
                identifyPlantBtn.disabled = false;
            } else {
                identifyPlantBtn.disabled = true;
            }
        });

        identifyPlantBtn.addEventListener('click', async () => {
            const file = plantImageInput.files[0];
            if (!file) {
                showError("يرجى اختيار صورة نبات أولاً.");
                return;
            }

            showLoading(true);
            identifyPlantBtn.disabled = true;

            const reader = new FileReader();
            reader.onload = async (event) => {
                const base64Image = event.target.result.split(',')[1];
                try {
                    const prompt = "ما هو هذا النبات؟ قدم وصفًا موجزًا، نصائح رعاية (ضوء، ماء، حرارة)، وفوائده، وما إذا كان صالحًا للأكل.";
                    const payload = {
                        contents: [{
                            parts: [
                                { text: prompt },
                                { inlineData: { mimeType: file.type, data: base64Image } }
                            ]
                        }],
                        generationConfig: {
                            temperature: 0.2,
                        },
                    };
                    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${GEMINI_API_KEY}`;
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    if (!response.ok) throw new Error('فشل في تحديد النبات.');
                    const result = await response.json();
                    const text = result?.candidates?.[0]?.content?.parts?.[0]?.text || 'لم يتم العثور على معلومات.';
                    plantResultDiv.innerHTML = `<div class="p-4 bg-orange-100 rounded-lg">${text.replace(/\n/g, '<br><br>')}</div>`;
                } catch (error) {
                    showError(error.message);
                    console.error(error);
                } finally {
                    showLoading(false);
                    identifyPlantBtn.disabled = false;
                }
            };
            reader.readAsDataURL(file);
        });

        // --- Plant Search Functions ---
        searchPlantBtn.addEventListener('click', async () => {
            const plantName = plantNameInput.value.trim();
            if (!plantName) {
                showError("يرجى إدخال اسم النبات.");
                return;
            }
            
            showLoading(true);
            try {
                const prompt = `قدم وصفًا موجزًا للنبات المسمى "${plantName}"، نصائح رعاية (ضوء، ماء، حرارة)، وفوائده، وما إذا كان صالحًا للأكل.`;
                const payload = {
                    contents: [{ parts: [{ text: prompt }] }],
                    generationConfig: {
                        temperature: 0.2,
                    },
                };
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${GEMINI_API_KEY}`;
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (!response.ok) throw new Error('فشل في البحث عن النبات.');
                const result = await response.json();
                const text = result?.candidates?.[0]?.content?.parts?.[0]?.text || 'لم يتم العثور على معلومات لهذا النبات.';
                plantSearchResultDiv.innerHTML = `<div class="p-4 bg-sky-100 rounded-lg">${text.replace(/\n/g, '<br><br>')}</div>`;
            } catch (error) {
                showError(error.message);
                console.error(error);
            } finally {
                showLoading(false);
            }
        });

        // --- Audio Playback Functions ---
        playAudioBtn.addEventListener('click', async () => {
            if (!lastAiAnalysis) {
                showError("لا يوجد تحليل صوتي للتشغيل.");
                return;
            }
            
            playAudioBtn.disabled = true;
            playAudioBtn.textContent = "جارٍ التشغيل...";
            
            try {
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-tts:generateContent?key=${GEMINI_API_KEY}`;
                const payload = {
                    contents: [{ parts: [{ text: lastAiAnalysis }] }],
                    generationConfig: {
                        responseModalities: ["AUDIO"],
                        speechConfig: {
                            voiceConfig: { prebuiltVoiceConfig: { voiceName: "Rasalgethi" } }
                        }
                    },
                    model: "gemini-2.5-flash-preview-tts"
                };

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) {
                    const error = await response.json();
                    throw new Error(error.error.message);
                }

                const result = await response.json();
                const audioPart = result?.candidates?.[0]?.content?.parts?.[0];
                const audioData = audioPart?.inlineData?.data;
                const mimeType = audioPart?.inlineData?.mimeType;

                if (audioData && mimeType && mimeType.startsWith("audio/")) {
                    const sampleRate = parseInt(mimeType.match(/rate=(\d+)/)[1], 10);
                    const pcmData = base64ToArrayBuffer(audioData);
                    const pcm16 = new Int16Array(pcmData);
                    const wavBlob = pcmToWav(pcm16, sampleRate);
                    const audioUrl = URL.createObjectURL(wavBlob);
                    
                    const audio = new Audio(audioUrl);
                    audio.play();
                    audio.onended = () => {
                         playAudioBtn.textContent = 'تشغيل التحليل الصوتي';
                         playAudioBtn.disabled = false;
                    };
                } else {
                    throw new Error('Invalid audio data received');
                }
            } catch (error) {
                showError("فشل تشغيل الصوت.");
                console.error(error);
            } finally {
                playAudioBtn.disabled = false;
            }
        });

        function base64ToArrayBuffer(base64) {
            const binary_string = window.atob(base64);
            const len = binary_string.length;
            const bytes = new Uint8Array(len);
            for (let i = 0; i < len; i++) {
                bytes[i] = binary_string.charCodeAt(i);
            }
            return bytes.buffer;
        }

        function pcmToWav(pcmData, sampleRate) {
            const buffer = new ArrayBuffer(44 + pcmData.length * 2);
            const view = new DataView(buffer);
            
            writeString(view, 0, 'RIFF');
            view.setUint32(4, 36 + pcmData.length * 2, true);
            writeString(view, 8, 'WAVE');
            writeString(view, 12, 'fmt ');
            view.setUint32(16, 16, true);
            view.setUint16(20, 1, true);
            view.setUint16(22, 1, true);
            view.setUint32(24, sampleRate, true);
            view.setUint32(28, sampleRate * 2, true);
            view.setUint16(32, 2, true);
            view.setUint16(34, 16, true);
            writeString(view, 36, 'data');
            view.setUint32(40, pcmData.length * 2, true);

            let offset = 44;
            for (let i = 0; i < pcmData.length; i++) {
                view.setInt16(offset, pcmData[i], true);
                offset += 2;
            }
            
            return new Blob([view], { type: 'audio/wav' });
        }

        function writeString(view, offset, string) {
            for (let i = 0; i < string.length; i++) {
                view.setUint8(offset + i, string.charCodeAt(i));
            }
        }
    </script>
</body>
</html>
