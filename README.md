<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ANILORE - Plataforma Completa</title>
    
    <!-- LIBRERÍAS EXTERNAS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- FIREBASE SDK -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, onAuthStateChanged, signOut, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";

        const firebaseConfig = JSON.parse(__firebase_config);
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);

        // Exponer funciones de autenticación al contexto global para que la UI las use
        window.firebaseAuth = auth;
        window.createUser = createUserWithEmailAndPassword;
        window.signIn = signInWithEmailAndPassword;
        window.logout = signOut;

        const initAuth = async () => {
            if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                await signInWithCustomToken(auth, __initial_auth_token);
            }
        };
        initAuth();

        onAuthStateChanged(auth, (user) => {
            const userBtn = document.getElementById('user-menu-btn');
            const userNameDisplay = document.getElementById('user-name-nav');
            const navProfile = document.getElementById('nav-profile');
            
            if (user) {
                userBtn.innerHTML = `<i class="fa-solid fa-right-from-bracket text-sm text-orange-500"></i>`;
                userBtn.onclick = () => window.handleLogout();
                userNameDisplay.innerText = user.email.split('@')[0];
                navProfile.classList.remove('hidden');
                window.closeAuthModal();
            } else {
                userBtn.innerHTML = `<i class="fa-solid fa-user text-sm"></i>`;
                userBtn.onclick = () => window.openAuthModal();
                userNameDisplay.innerText = "Invitado";
                navProfile.classList.add('hidden');
                if (window.currentSection === 'profile') window.showSection('home');
            }
        });
    </script>
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --primary: #f26522; --bg-dark: #09090b; --card-bg: #18181b; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: var(--bg-dark); color: #f4f4f5; margin: 0; overflow-x: hidden; }
        
        /* Navegación y UI Base */
        .glass-nav { background: rgba(9, 9, 11, 0.95); backdrop-filter: blur(12px); border-bottom: 1px solid rgba(255, 255, 255, 0.05); }
        .manhwa-card { border-radius: 16px; overflow: hidden; transition: 0.3s; background: var(--card-bg); border: 1px solid rgba(255, 255, 255, 0.03); }
        .manhwa-card:hover { transform: translateY(-5px); border-color: var(--primary); box-shadow: 0 10px 25px rgba(242, 101, 34, 0.2); }
        .card-gradient { background: linear-gradient(to top, rgba(0,0,0,0.9) 0%, rgba(0,0,0,0.2) 60%, transparent 100%); }
        .alphabet-btn { background: #1f1f23; color: #a1a1aa; width: 34px; height: 34px; display: flex; align-items: center; justify-content: center; border-radius: 8px; font-weight: 700; font-size: 12px; transition: 0.2s; cursor: pointer; }
        .alphabet-btn:hover, .alphabet-btn.active { background: var(--primary); color: white; }
        .custom-input { background: #121214; border: 1px solid #27272a; border-radius: 10px; padding: 10px 14px; width: 100%; outline: none; color: white; transition: 0.2s; }
        .custom-input:focus { border-color: var(--primary); box-shadow: 0 0 0 2px rgba(242, 101, 34, 0.1); }
        
        .hidden-section { display: none !important; }
        
        /* Modales y Formularios */
        #auth-modal { background: rgba(0,0,0,0.85); backdrop-filter: blur(10px); }
        .file-drop-area { border: 2px dashed #27272a; border-radius: 16px; min-height: 150px; display: flex; flex-direction: column; align-items: center; justify-content: center; cursor: pointer; position: relative; overflow: hidden; transition: 0.3s; }
        .file-drop-area:hover { border-color: var(--primary); background: rgba(242, 101, 34, 0.05); }
        .preview-img { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: cover; z-index: 5; }
        
        /* Animaciones */
        #custom-toast { position: fixed; bottom: 20px; right: 20px; background: var(--primary); color: white; padding: 12px 24px; border-radius: 12px; font-weight: bold; z-index: 1000; transform: translateY(100px); transition: 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        #custom-toast.show { transform: translateY(0); }
        .animate-fade-in { animation: fadeIn 0.4s ease-out forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .ai-loading { animation: pulse 1.5s cubic-bezier(0.4, 0, 0.6, 1) infinite; }
        @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: .5; } }
        
        /* Componentes de Detalles/Lector */
        .chapter-item { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); transition: 0.2s; }
        .chapter-item:hover { background: var(--primary); color: white; }
        .tab-btn.active { border-bottom: 2px solid var(--primary); color: var(--primary); }
    </style>
</head>
<body>

    <!-- NOTIFICACIONES -->
    <div id="custom-toast">Acción completada</div>

    <!-- MODAL DE AUTENTICACIÓN -->
    <div id="auth-modal" class="fixed inset-0 z-[100] hidden flex items-center justify-center p-6">
        <div class="bg-zinc-900 w-full max-w-md rounded-[32px] border border-zinc-800 p-8 relative overflow-hidden shadow-2xl">
            <button onclick="closeAuthModal()" class="absolute top-6 right-6 text-zinc-500 hover:text-white">
                <i class="fa-solid fa-xmark text-xl"></i>
            </button>
            <!-- Vista Login -->
            <div id="auth-login-view">
                <h2 class="text-3xl font-black uppercase mb-2">Entrar</h2>
                <p class="text-zinc-500 text-sm mb-6">Inicia sesión en Anilore.</p>
                <div class="space-y-4">
                    <input type="email" id="login-email" placeholder="Correo electrónico" class="custom-input">
                    <input type="password" id="login-pass" placeholder="Contraseña" class="custom-input">
                    <button onclick="handleLoginAction()" class="w-full bg-orange-500 hover:bg-orange-600 py-4 rounded-2xl font-black uppercase text-sm text-white transition shadow-lg shadow-orange-500/20">Iniciar Sesión</button>
                    <p class="text-center text-xs text-zinc-500 mt-4">¿No tienes cuenta? <button onclick="toggleAuthView('signup')" class="text-orange-500 font-bold">Regístrate</button></p>
                </div>
            </div>
            <!-- Vista Registro -->
            <div id="auth-signup-view" class="hidden">
                <h2 class="text-3xl font-black uppercase mb-2">Registro</h2>
                <p class="text-zinc-500 text-sm mb-6">Únete a la comunidad.</p>
                <div class="space-y-4">
                    <input type="text" id="signup-user" placeholder="Nombre de usuario" class="custom-input">
                    <input type="email" id="signup-email" placeholder="Correo electrónico" class="custom-input">
                    <input type="password" id="signup-pass" placeholder="Contraseña" class="custom-input">
                    <button onclick="handleSignupAction()" class="w-full bg-white text-black hover:bg-orange-500 hover:text-white py-4 rounded-2xl font-black uppercase text-sm transition shadow-lg">Crear Cuenta</button>
                    <p class="text-center text-xs text-zinc-500 mt-4">¿Ya tienes cuenta? <button onclick="toggleAuthView('login')" class="text-orange-500 font-bold">Inicia Sesión</button></p>
                </div>
            </div>
        </div>
    </div>

    <!-- NAVEGACIÓN -->
    <nav class="glass-nav fixed w-full top-0 z-50 px-6 py-2 flex justify-between items-center">
        <div class="flex items-center gap-3 cursor-pointer" onclick="showSection('home')">
            <img src="image_6fb3851d.jpg" alt="Logo" class="h-10 w-auto rounded-lg">
            <div class="hidden lg:block leading-none">
                <span class="text-xl font-black tracking-tighter text-white">ANILORE</span>
                <p class="text-[9px] text-orange-500 font-bold uppercase tracking-widest">Manga & Anime</p>
            </div>
        </div>

        <div class="hidden md:flex gap-8 font-bold text-zinc-400 text-xs uppercase tracking-widest">
            <button id="nav-home" onclick="showSection('home')" class="text-orange-500 transition">Inicio</button>
            <button id="nav-library" onclick="showSection('library')" class="hover:text-orange-500 transition">Biblioteca</button>
            <button id="nav-upload" onclick="showSection('upload')" class="hover:text-orange-500 transition">Publicar</button>
            <button id="nav-profile" onclick="showSection('profile')" class="hover:text-orange-500 transition hidden">Mi Perfil</button>
        </div>

        <div class="flex items-center gap-4">
            <div class="relative hidden sm:block">
                <input type="text" id="main-search" oninput="handleSearch(this.value)" placeholder="Buscar obra..." class="bg-zinc-900 border border-zinc-800 rounded-full px-5 py-2 text-xs focus:outline-none w-40 focus:w-64 transition-all text-white focus:border-orange-500">
                <i class="fa-solid fa-magnifying-glass absolute right-4 top-2.5 text-zinc-500 text-[10px]"></i>
            </div>
            <button onclick="getAiRecommendation()" title="Recomendación Inteligente" class="w-10 h-10 rounded-full bg-zinc-800 flex items-center justify-center border border-zinc-700 text-orange-500 hover:scale-110 transition">
                <i class="fa-solid fa-sparkles"></i>
            </button>
            <div class="flex items-center gap-3 ml-2">
                <span id="user-name-nav" class="hidden lg:block text-[10px] font-bold uppercase text-zinc-500 tracking-wider">Invitado</span>
                <button id="user-menu-btn" onclick="openAuthModal()" class="w-10 h-10 rounded-full bg-zinc-800 flex items-center justify-center border border-zinc-700 hover:border-orange-500 transition">
                    <i class="fa-solid fa-user text-sm"></i>
                </button>
            </div>
        </div>
    </nav>

    <!-- SECCIÓN DE INICIO -->
    <div id="home-section" class="pt-24 pb-20 px-6 md:px-12">
        <div id="hero-container" class="relative h-[400px] rounded-[40px] overflow-hidden shadow-2xl group cursor-pointer transition-all duration-700" onclick="openHeroDetails()">
            <img id="hero-bg" src="https://images.unsplash.com/photo-1614850523296-d8c1af93d400?q=80&w=2070" class="absolute inset-0 w-full h-full object-cover transition-transform duration-1000 group-hover:scale-105">
            <div class="absolute inset-0 bg-gradient-to-t from-black via-black/30 to-transparent"></div>
            <div class="relative z-10 h-full flex flex-col justify-end p-8 md:p-12 max-w-2xl">
                <span id="hero-tag" class="bg-orange-500 text-white text-[10px] font-black px-3 py-1 rounded-full w-fit uppercase tracking-widest mb-4">Bienvenido</span>
                <h1 id="hero-title" class="text-4xl md:text-6xl font-black italic uppercase leading-none text-white drop-shadow-lg">Explora Anilore</h1>
                <p id="hero-desc" class="text-zinc-200 text-sm md:text-base mt-4 line-clamp-2 drop-shadow-md">Publica tus propias obras y gestiona tus capítulos.</p>
            </div>
        </div>

        <div id="ai-suggestion-box" class="hidden mt-6">
            <div class="bg-orange-500/10 border border-orange-500/20 p-6 rounded-[24px] flex flex-col md:flex-row gap-6 items-center">
                <div class="flex-1">
                    <span class="text-[10px] font-black uppercase text-orange-500 tracking-widest flex items-center gap-2">
                        <i class="fa-solid fa-wand-magic-sparkles"></i> Sugerencia para ti
                    </span>
                    <h3 id="ai-suggestion-title" class="text-xl font-bold mt-1 text-white">...</h3>
                    <p id="ai-suggestion-reason" class="text-zinc-400 text-sm mt-2 italic">Analizando preferencias...</p>
                </div>
                <button onclick="document.getElementById('ai-suggestion-box').classList.add('hidden')" class="text-zinc-500 text-xs font-bold uppercase hover:text-white transition">Cerrar</button>
            </div>
        </div>

        <h2 id="home-title" class="text-2xl font-black uppercase mb-8 mt-10 flex items-center gap-3">
            <span class="w-8 h-1 bg-orange-500"></span> Últimas Novedades
        </h2>
        <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 gap-6" id="home-grid"></div>
    </div>

    <!-- SECCIÓN DE BIBLIOTECA -->
    <div id="library-section" class="pt-28 px-6 md:px-12 pb-20 hidden-section">
        <h1 class="text-3xl font-black mb-8 uppercase text-white">Biblioteca Completa</h1>
        <div class="flex flex-wrap gap-1.5 mb-8 bg-zinc-900/40 p-3 rounded-2xl border border-zinc-800 overflow-x-auto">
            <button class="alphabet-btn active" id="alpha-all" onclick="filterByLetter('#')">#</button>
            <script>
                "ABCDEFGHIJKLMNÑOPQRSTUVWXYZ".split("").forEach(l => {
                    document.write(`<button class="alphabet-btn" onclick="filterByLetter('${l}')">${l}</button>`);
                });
            </script>
        </div>
        <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 gap-6" id="library-grid"></div>
    </div>

    <!-- SECCIÓN PERFIL -->
    <div id="profile-section" class="pt-28 px-6 md:px-12 pb-20 hidden-section">
        <div class="flex items-center gap-6 mb-12">
            <div class="w-24 h-24 rounded-full bg-orange-500 flex items-center justify-center text-4xl font-black text-white" id="profile-avatar">?</div>
            <div>
                <h1 id="profile-name" class="text-3xl md:text-4xl font-black uppercase tracking-tighter">Usuario</h1>
                <p class="text-zinc-500 text-sm">Coleccionista de Anilore</p>
            </div>
        </div>
        <div class="flex gap-8 border-b border-zinc-800 mb-8">
            <button onclick="filterProfile('like')" id="tab-like" class="tab-btn active pb-4 font-bold uppercase text-xs tracking-widest transition">Me Gusta</button>
            <button onclick="filterProfile('save')" id="tab-save" class="tab-btn pb-4 font-bold uppercase text-xs tracking-widest transition">Guardados</button>
            <button onclick="filterProfile('done')" id="tab-done" class="tab-btn pb-4 font-bold uppercase text-xs tracking-widest transition">Terminados</button>
        </div>
        <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 gap-6" id="profile-grid"></div>
    </div>

    <!-- SECCIÓN DE DETALLES DE OBRA -->
    <div id="details-section" class="pt-28 px-6 md:px-12 pb-20 hidden-section">
        <button onclick="goBackFromDetails()" class="mb-8 flex items-center gap-2 text-zinc-500 hover:text-white transition font-bold uppercase text-xs">
            <i class="fa-solid fa-arrow-left"></i> Volver
        </button>

        <div class="flex flex-col lg:flex-row gap-12">
            <div class="lg:w-2/3">
                <div class="flex flex-col md:flex-row gap-8">
                    <img id="det-img" src="" class="w-full md:w-64 h-96 object-cover rounded-[32px] shadow-2xl border border-zinc-800">
                    <div class="flex-1">
                        <span id="det-tag" class="text-orange-500 font-black text-xs uppercase tracking-widest">Género</span>
                        <h1 id="det-title" class="text-4xl md:text-5xl font-black uppercase italic my-4 text-white">Título</h1>
                        <p id="det-sinopsis" class="text-zinc-400 leading-relaxed text-sm md:text-base">Sinopsis...</p>
                        
                        <div class="flex flex-wrap gap-3 mt-8">
                            <button id="btn-like" onclick="userAction('like')" class="flex items-center gap-2 px-6 py-3 rounded-2xl bg-zinc-900 border border-zinc-800 hover:border-orange-500 transition font-bold text-xs uppercase">
                                <i class="fa-solid fa-heart"></i> Me Gusta
                            </button>
                            <button id="btn-save" onclick="userAction('save')" class="flex items-center gap-2 px-6 py-3 rounded-2xl bg-zinc-900 border border-zinc-800 hover:border-orange-500 transition font-bold text-xs uppercase">
                                <i class="fa-solid fa-bookmark"></i> Guardar
                            </button>
                            <button id="btn-done" onclick="userAction('done')" class="flex items-center gap-2 px-6 py-3 rounded-2xl bg-zinc-900 border border-zinc-800 hover:border-orange-500 transition font-bold text-xs uppercase">
                                <i class="fa-solid fa-check-double"></i> Terminado
                            </button>
                        </div>
                    </div>
                </div>
            </div>

            <div class="lg:w-1/3">
                <div class="flex justify-between items-center mb-6">
                    <h3 class="text-xl font-black uppercase flex items-center gap-2">
                        <i class="fa-solid fa-list-ul text-orange-500"></i> Episodios
                    </h3>
                </div>
                <div id="det-episodes" class="space-y-3 max-h-[500px] overflow-y-auto pr-2 scrollbar-hide"></div>
            </div>
        </div>
    </div>

    <!-- SECCIÓN DE PUBLICACIÓN -->
    <div id="upload-section" class="pt-28 px-6 md:px-12 pb-20 hidden-section">
        <div class="max-w-2xl mx-auto">
            <div class="flex justify-center gap-2 mb-8 bg-zinc-900 p-1.5 rounded-2xl border border-zinc-800">
                <button onclick="switchTab('obra')" id="tab-obra" class="alphabet-btn !w-1/2 !h-12 active">Nueva Obra</button>
                <button onclick="switchTab('capitulo')" id="tab-capitulo" class="alphabet-btn !w-1/2 !h-12">Nuevo Capítulo</button>
            </div>

            <div id="form-obra" class="space-y-6 bg-zinc-900/60 p-8 rounded-[32px] border border-zinc-800">
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <div class="md:col-span-1">
                        <div class="file-drop-area h-[180px]" onclick="document.getElementById('in-portada').click()">
                            <input type="file" id="in-portada" hidden accept="image/*" onchange="previewImg(this, 'pre-portada')">
                            <img id="pre-portada" class="preview-img hidden">
                            <div id="ico-portada" class="z-10 text-center"><i class="fa-solid fa-image text-2xl mb-1 text-zinc-500"></i><p class="text-[10px] font-bold uppercase text-zinc-500">Portada</p></div>
                        </div>
                    </div>
                    <div class="md:col-span-2 space-y-4">
                        <input type="text" id="obra-titulo" placeholder="Título de la Obra" class="custom-input">
                        <select id="obra-genero" class="custom-input">
                            <option value="Acción">Acción</option>
                            <option value="Aventura">Aventura</option>
                            <option value="Drama">Drama</option>
                            <option value="Comedia">Comedia</option>
                            <option value="Terror">Terror</option>
                            <option value="Fantasía">Fantasía</option>
                        </select>
                        <button onclick="generateSinopsis()" id="ai-btn-sinopsis" class="text-[10px] bg-zinc-800 hover:bg-zinc-700 text-orange-500 font-bold px-4 py-2 rounded-lg border border-zinc-700 flex items-center gap-2 transition w-full justify-center">
                            <i class="fa-solid fa-sparkles"></i> GENERAR SINOPSIS CON IA ✨
                        </button>
                    </div>
                </div>
                <textarea id="obra-sinopsis" placeholder="Escribe o genera una sinopsis..." class="custom-input h-32 resize-none"></textarea>
                <button onclick="handlePublicarObra()" class="w-full bg-orange-500 hover:bg-orange-600 transition py-4 rounded-2xl font-black uppercase text-sm shadow-lg shadow-orange-500/20 text-white">Publicar Obra</button>
            </div>

            <div id="form-capitulo" class="space-y-6 bg-zinc-900/60 p-8 rounded-[32px] border border-zinc-800 hidden">
                <div class="grid grid-cols-2 gap-4">
                    <select id="cap-obra-select" class="custom-input"><option value="">Elegir Obra...</option></select>
                    <input type="number" id="cap-numero" placeholder="Capítulo Nº" class="custom-input">
                </div>
                <div class="file-drop-area h-[180px]" onclick="document.getElementById('in-paginas').click()">
                    <input type="file" id="in-paginas" hidden multiple accept="image/*">
                    <i class="fa-solid fa-images text-3xl mb-2 text-zinc-500"></i>
                    <p id="paginas-status" class="text-xs font-bold uppercase text-zinc-500">Subir Páginas</p>
                </div>
                <button onclick="handleSubirCapitulo()" class="w-full bg-orange-500 hover:bg-orange-600 transition py-4 rounded-2xl font-black uppercase text-sm shadow-lg shadow-orange-500/20 text-white">Subir Capítulo</button>
            </div>
        </div>
    </div>

    <!-- SECCIÓN LECTOR DE MANHWA -->
    <div id="reader-section" class="pt-20 pb-10 hidden-section bg-black min-h-screen">
        <div class="max-w-3xl mx-auto w-full">
            <div class="text-center mb-8 px-4">
                <h2 id="reader-title" class="text-sm font-bold text-zinc-500 uppercase tracking-widest mb-1">Obra</h2>
                <h1 id="reader-chap-num" class="text-3xl font-black text-orange-500 mb-6">Capítulo X</h1>
                
                <div class="flex justify-center items-center gap-3">
                    <button id="btn-prev-top" onclick="navigateChap(-1)" class="bg-zinc-900 text-zinc-400 px-4 py-2 rounded-xl font-bold text-xs uppercase transition border border-zinc-800 hover:bg-orange-500 hover:text-white"><i class="fa-solid fa-chevron-left mr-1"></i> Anterior</button>
                    <button onclick="showSection('details')" class="bg-zinc-800 hover:bg-zinc-700 text-white px-6 py-2 rounded-xl font-bold text-xs uppercase transition border border-zinc-700"><i class="fa-solid fa-list mr-1"></i> Obra</button>
                    <button id="btn-next-top" onclick="navigateChap(1)" class="bg-zinc-900 text-zinc-400 px-4 py-2 rounded-xl font-bold text-xs uppercase transition border border-zinc-800 hover:bg-orange-500 hover:text-white">Siguiente <i class="fa-solid fa-chevron-right ml-1"></i></button>
                </div>
            </div>

            <div id="reader-images" class="flex flex-col items-center w-full"></div>

            <div class="flex justify-center items-center gap-3 mt-12 px-4">
                <button id="btn-prev-bot" onclick="navigateChap(-1)" class="bg-zinc-900 text-zinc-400 px-4 py-3 rounded-xl font-bold text-xs uppercase transition border border-zinc-800 hover:bg-orange-500 hover:text-white"><i class="fa-solid fa-chevron-left mr-1"></i> Anterior</button>
                <button onclick="showSection('details')" class="bg-zinc-800 hover:bg-zinc-700 text-white px-6 py-3 rounded-xl font-bold text-xs uppercase transition border border-zinc-700"><i class="fa-solid fa-list mr-1"></i> Obra</button>
                <button id="btn-next-bot" onclick="navigateChap(1)" class="bg-zinc-900 text-zinc-400 px-4 py-3 rounded-xl font-bold text-xs uppercase transition border border-zinc-800 hover:bg-orange-500 hover:text-white">Siguiente <i class="fa-solid fa-chevron-right ml-1"></i></button>
            </div>
        </div>
    </div>

    <!-- SCRIPTS DE LÓGICA (Unificado y seguro) -->
    <script>
        const apiKey = ""; 

        // Base de datos y estado de la aplicación
        window.manhwas = [];
        window.userData = { like: [], save: [], done: [] };
        window.currentSection = 'home';
        window.previousSection = 'home';
        window.activeWorkIndex = -1;
        window.currentReadChapter = -1;
        window.currentSearchTerm = "";

        // --- AUTH UI ---
        window.openAuthModal = function() {
            document.getElementById('auth-modal').classList.remove('hidden');
            document.body.style.overflow = 'hidden';
        };
        
        window.closeAuthModal = function() {
            document.getElementById('auth-modal').classList.add('hidden');
            document.body.style.overflow = 'auto';
        };
        
        window.toggleAuthView = function(view) {
            document.getElementById('auth-login-view').classList.toggle('hidden', view === 'signup');
            document.getElementById('auth-signup-view').classList.toggle('hidden', view === 'login');
        };

        window.handleSignupAction = async function() {
            const email = document.getElementById('signup-email').value;
            const pass = document.getElementById('signup-pass').value;
            const user = document.getElementById('signup-user').value;
            if (!email || !pass || !user) return window.showToast("Faltan datos de registro");
            try {
                await window.createUser(window.firebaseAuth, email, pass);
                window.showToast("Cuenta creada con éxito");
            } catch (error) { window.showToast("Error: " + error.message); }
        };

        window.handleLoginAction = async function() {
            const email = document.getElementById('login-email').value;
            const pass = document.getElementById('login-pass').value;
            if (!email || !pass) return window.showToast("Ingresa tus credenciales");
            try {
                await window.signIn(window.firebaseAuth, email, pass);
                window.showToast("Sesión iniciada");
            } catch (error) { window.showToast("Error de acceso"); }
        };

        window.handleLogout = async function() {
            try {
                await window.logout(window.firebaseAuth);
                window.showToast("Sesión cerrada");
            } catch (error) { console.error(error); }
        };

        // --- NAVEGACIÓN Y RENDERIZADO ---
        window.showSection = function(id) {
            if (window.currentSection !== 'details' && window.currentSection !== 'reader') {
                window.previousSection = window.currentSection;
            }
            window.currentSection = id;
            
            const sections = ['home-section', 'library-section', 'upload-section', 'details-section', 'profile-section', 'reader-section'];
            sections.forEach(s => document.getElementById(s).classList.toggle('hidden-section', !s.startsWith(id)));
            
            const navs = ['nav-home', 'nav-library', 'nav-upload', 'nav-profile'];
            navs.forEach(n => {
                const el = document.getElementById(n);
                if(el) el.classList.toggle('text-orange-500', n.endsWith(id));
            });

            if (id === 'upload') window.updateObraSelect();
            if (id === 'profile') window.filterProfile('like');
            
            window.scrollTo(0, 0);
        };

        window.render = function(id, list) {
            const el = document.getElementById(id);
            if (!el) return;
            el.innerHTML = list.length ? list.map((m) => `
                <div class="manhwa-card group cursor-pointer animate-fade-in" onclick="window.openDetails(${window.manhwas.indexOf(m)})">
                    <div class="aspect-[3/4.2] relative">
                        <img src="${m.img}" class="w-full h-full object-cover transition duration-500 group-hover:scale-110">
                        <div class="absolute inset-0 card-gradient"></div>
                        <div class="absolute bottom-4 left-4 right-4">
                            <h3 class="font-black text-sm uppercase italic line-clamp-1 text-white">${m.title}</h3>
                            <p class="text-[10px] text-orange-500 font-bold">Cap. ${m.numCh}</p>
                        </div>
                    </div>
                </div>
            `).join('') : `<p class="col-span-full text-center opacity-20 py-20 font-bold uppercase text-zinc-500">No hay resultados</p>`;
        };

        // --- BÚSQUEDA Y FILTROS ---
        window.handleSearch = function(val) {
            window.currentSearchTerm = val.toLowerCase().trim();
            const filtered = window.manhwas.filter(m => m.title.toLowerCase().includes(window.currentSearchTerm));
            window.render('home-grid', filtered);
            window.render('library-grid', filtered);
            
            const homeTitle = document.getElementById('home-title');
            if (window.currentSearchTerm.length > 0) {
                homeTitle.innerHTML = `<span class="w-8 h-1 bg-orange-500"></span> Resultados para: "${val}"`;
            } else {
                homeTitle.innerHTML = `<span class="w-8 h-1 bg-orange-500"></span> Últimas Novedades`;
            }
        };

        window.filterByLetter = function(l) {
            document.querySelectorAll('.alphabet-btn').forEach(b => b.classList.remove('active'));
            if(l === '#') {
                document.getElementById('alpha-all').classList.add('active');
                window.handleSearch(window.currentSearchTerm);
            } else {
                const btn = Array.from(document.querySelectorAll('.alphabet-btn')).find(b => b.innerText === l);
                if(btn) btn.classList.add('active');
                const filtered = window.manhwas.filter(m => 
                    m.title.toUpperCase().startsWith(l) && 
                    m.title.toLowerCase().includes(window.currentSearchTerm)
                );
                window.render('library-grid', filtered);
            }
            window.showSection('library');
        };

        window.filterProfile = function(type) {
            ['tab-like', 'tab-save', 'tab-done'].forEach(b => document.getElementById(b).classList.toggle('active', b.endsWith(type)));
            const filtered = window.manhwas.filter(m => window.userData[type].includes(m.title));
            window.render('profile-grid', filtered);
        };

        // --- DETALLES Y LECTOR ---
        window.openHeroDetails = function() {
            if (window.manhwas.length > 0) window.openDetails(0);
        };

        window.openDetails = function(index) {
            window.activeWorkIndex = index;
            const obra = window.manhwas[index];
            if(!obra) return;
            
            document.getElementById('det-img').src = obra.img;
            document.getElementById('det-title').innerText = obra.title;
            document.getElementById('det-tag').innerText = obra.tag;
            document.getElementById('det-sinopsis').innerText = obra.sinopsis;
            
            window.refreshChapters();
            window.updateActionButtons();
            window.showSection('details');
        };

        window.goBackFromDetails = function() {
            if (window.previousSection === 'profile') window.showSection('profile');
            else if (window.previousSection === 'library') window.showSection('library');
            else window.showSection('home');
        };

        window.refreshChapters = function() {
            const obra = window.manhwas[window.activeWorkIndex];
            const container = document.getElementById('det-episodes');
            container.innerHTML = "";
            for (let i = obra.numCh; i >= 1; i--) {
                container.innerHTML += `
                    <div onclick="window.openReader(${window.activeWorkIndex}, ${i})" class="chapter-item p-4 rounded-xl flex justify-between items-center cursor-pointer group">
                        <span class="font-bold text-xs">Capítulo ${i}</span>
                        <i class="fa-solid fa-play text-[10px] text-orange-500 opacity-0 group-hover:opacity-100 transition"></i>
                    </div>`;
            }
        };

        window.userAction = function(type) {
            if (!window.firebaseAuth.currentUser) return window.showToast("Inicia sesión para guardar");
            const workId = window.manhwas[window.activeWorkIndex].title;
            const idx = window.userData[type].indexOf(workId);
            if (idx > -1) {
                window.userData[type].splice(idx, 1);
                window.showToast("Eliminado");
            } else {
                window.userData[type].push(workId);
                const msgs = { like: '¡Me gusta!', save: 'Guardado', done: 'Terminado' };
                window.showToast(msgs[type]);
            }
            window.updateActionButtons();
        };

        window.updateActionButtons = function() {
            if(window.activeWorkIndex < 0) return;
            const workId = window.manhwas[window.activeWorkIndex].title;
            ['like', 'save', 'done'].forEach(t => {
                const btn = document.getElementById(`btn-${t}`);
                const active = window.userData[t].includes(workId);
                btn.classList.toggle('bg-orange-500', active);
                btn.classList.toggle('text-white', active);
                btn.classList.toggle('bg-zinc-900', !active);
                btn.classList.toggle('border-orange-500', !active);
            });
        };

        window.openReader = function(workIndex, chapNum) {
            window.activeWorkIndex = workIndex;
            window.currentReadChapter = chapNum;
            const obra = window.manhwas[workIndex];

            document.getElementById('reader-title').innerText = obra.title;
            document.getElementById('reader-chap-num').innerText = `Capítulo ${chapNum}`;

            const isFirst = chapNum === 1;
            const isLast = chapNum === obra.numCh;

            const toggleBtn = (id, disable) => {
                const btn = document.getElementById(id);
                if (disable) {
                    btn.classList.add('opacity-30', 'pointer-events-none');
                    btn.classList.remove('hover:bg-orange-500', 'hover:text-white');
                } else {
                    btn.classList.remove('opacity-30', 'pointer-events-none');
                    btn.classList.add('hover:bg-orange-500', 'hover:text-white');
                }
            };

            toggleBtn('btn-prev-top', isFirst);
            toggleBtn('btn-prev-bot', isFirst);
            toggleBtn('btn-next-top', isLast);
            toggleBtn('btn-next-bot', isLast);

            const imgContainer = document.getElementById('reader-images');
            imgContainer.innerHTML = '';
            // 3 Imágenes verticales simulando Manhwa
            for(let i=1; i<=3; i++) {
                imgContainer.innerHTML += `<img src="https://via.placeholder.com/800x1200/18181b/f26522?text=Pagina+${i}+-+Capitulo+${chapNum}+-+${encodeURIComponent(obra.title)}" class="w-full h-auto object-cover block">`;
            }

            window.showSection('reader');
        };

        window.navigateChap = function(dir) {
            const obra = window.manhwas[window.activeWorkIndex];
            const targetChap = window.currentReadChapter + dir;
            if(targetChap >= 1 && targetChap <= obra.numCh) {
                window.openReader(window.activeWorkIndex, targetChap);
            }
        };

        // --- PUBLICACIÓN Y FORMULARIOS ---
        window.switchTab = function(type) {
            const isObra = type === 'obra';
            document.getElementById('tab-obra').classList.toggle('active', isObra);
            document.getElementById('tab-capitulo').classList.toggle('active', !isObra);
            document.getElementById('form-obra').classList.toggle('hidden', !isObra);
            document.getElementById('form-capitulo').classList.toggle('hidden', isObra);
            if(!isObra) window.updateObraSelect();
        };

        window.previewImg = function(input, preId) {
            const file = input.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = e => {
                    document.getElementById(preId).src = e.target.result;
                    document.getElementById(preId).classList.remove('hidden');
                    document.getElementById('ico-portada').classList.add('opacity-0');
                };
                reader.readAsDataURL(file);
            }
        };

        window.handlePublicarObra = function() {
            if (!window.firebaseAuth.currentUser) return window.showToast("Inicia sesión para publicar");
            const titulo = document.getElementById('obra-titulo').value.trim();
            const genero = document.getElementById('obra-genero').value;
            const sinopsis = document.getElementById('obra-sinopsis').value.trim();
            const imgPreview = document.getElementById('pre-portada').src;

            if (!titulo || !sinopsis) return window.showToast("Completa los campos");

            const nuevaObra = {
                title: titulo,
                numCh: 1,
                tag: genero,
                sinopsis: sinopsis,
                img: imgPreview && imgPreview.startsWith('data:') ? imgPreview : "https://images.unsplash.com/photo-1614850523296-d8c1af93d400?q=80&w=2070"
            };

            window.manhwas.unshift(nuevaObra);
            window.handleSearch(window.currentSearchTerm);
            window.updateHeroBanner();
            window.showToast("¡Obra publicada!");
            window.resetObraForm();
            window.openDetails(0);
        };

        window.handleSubirCapitulo = function() {
            if (!window.firebaseAuth.currentUser) return window.showToast("Inicia sesión para publicar");
            const obraIndex = document.getElementById('cap-obra-select').value;
            const numCap = document.getElementById('cap-numero').value;
            const files = document.getElementById('in-paginas').files;

            if (obraIndex === "" || !numCap || files.length === 0) return window.showToast("Faltan datos");

            const idx = parseInt(obraIndex);
            if(parseInt(numCap) > window.manhwas[idx].numCh) {
                window.manhwas[idx].numCh = parseInt(numCap);
            }
            
            window.handleSearch(window.currentSearchTerm);
            window.showToast(`¡Capítulo ${numCap} subido!`);
            window.resetCapituloForm();
            window.openDetails(idx);
        };

        window.updateObraSelect = function() {
            const select = document.getElementById('cap-obra-select');
            if (!select) return;
            select.innerHTML = '<option value="">Elegir Obra...</option>';
            window.manhwas.forEach((m, index) => {
                const opt = document.createElement('option');
                opt.value = index;
                opt.textContent = m.title;
                select.appendChild(opt);
            });
        };

        window.resetObraForm = function() {
            document.getElementById('obra-titulo').value = "";
            document.getElementById('obra-sinopsis').value = "";
            const pre = document.getElementById('pre-portada');
            pre.classList.add('hidden');
            pre.src = "";
            document.getElementById('ico-portada').classList.remove('opacity-0');
        };

        window.resetCapituloForm = function() {
            document.getElementById('cap-obra-select').value = "";
            document.getElementById('cap-numero').value = "";
            document.getElementById('in-paginas').value = "";
            document.getElementById('paginas-status').innerText = "Subir Páginas";
        };

        window.updateHeroBanner = function() {
            const heroBg = document.getElementById('hero-bg');
            if (!heroBg) return;
            if (window.manhwas.length === 0) {
                heroBg.src = "https://images.unsplash.com/photo-1614850523296-d8c1af93d400?q=80&w=2070";
                document.getElementById('hero-title').innerText = "Explora Anilore";
                document.getElementById('hero-desc').innerText = "Publica y lee.";
                return;
            }
            const latest = window.manhwas[0];
            heroBg.src = latest.img;
            document.getElementById('hero-title').innerText = latest.title;
            document.getElementById('hero-desc').innerText = latest.sinopsis;
        };

        window.showToast = function(msg) {
            const toast = document.getElementById('custom-toast');
            toast.innerText = msg;
            toast.classList.add('show');
            setTimeout(() => toast.classList.remove('show'), 3000);
        };

        document.getElementById('in-paginas').addEventListener('change', function(e) {
            const count = e.target.files.length;
            document.getElementById('paginas-status').innerText = count > 0 ? `${count} seleccionadas` : "Subir Páginas";
        });

        // --- FUNCIONES IA (Gemini) ---
        window.callGemini = async function(prompt) {
            let retries = 0;
            while (retries < 3) {
                try {
                    const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
                    });
                    const data = await response.json();
                    return data.candidates?.[0]?.content?.parts?.[0]?.text;
                } catch (e) {
                    retries++;
                    await new Promise(res => setTimeout(res, 1000));
                }
            }
            throw new Error("IA desconectada");
        };

        window.generateSinopsis = async function() {
            const titulo = document.getElementById('obra-titulo').value;
            const genero = document.getElementById('obra-genero').value;
            const btn = document.getElementById('ai-btn-sinopsis');
            const area = document.getElementById('obra-sinopsis');
            if (!titulo) return window.showToast("Escribe un título primero");

            btn.innerHTML = '<i class="fa-solid fa-spinner fa-spin"></i> IMAGINANDO...';
            btn.classList.add('ai-loading');
            try {
                const result = await window.callGemini(`Crea una sinopsis (100 palabras) para un manhwa llamado "${titulo}" del género "${genero}". Tono épico. Español.`);
                area.value = result.trim();
            } catch (err) { window.showToast("Error de conexión"); }
            btn.innerHTML = '<i class="fa-solid fa-sparkles"></i> GENERAR SINOPSIS ✨';
            btn.classList.remove('ai-loading');
        };

        window.getAiRecommendation = async function() {
            const box = document.getElementById('ai-suggestion-box');
            const titleEl = document.getElementById('ai-suggestion-title');
            const reasonEl = document.getElementById('ai-suggestion-reason');

            box.classList.remove('hidden');
            titleEl.innerText = "Pensando...";
            reasonEl.innerText = "Analizando base de datos...";

            try {
                if (window.manhwas.length === 0) {
                    titleEl.innerText = "Aún no hay obras";
                    reasonEl.innerText = "¡Publica tu primera obra!";
                    return;
                }
                const result = await window.callGemini(`Analiza: ${window.manhwas.map(m=>m.title).join(", ")}. Sugiere un título nuevo y razón. Formato: Título: [Nombre] | Razón: [Texto]`);
                const [titulo, razon] = result.split('|');
                titleEl.innerText = titulo.replace('Título:', '').trim();
                reasonEl.innerText = razon.replace('Razón:', '').trim();
            } catch (err) {
                titleEl.innerText = "Recomendación temporal";
                reasonEl.innerText = "¡Prueba algo de acción!";
            }
        };

        // --- INICIALIZACIÓN ---
        window.onload = () => {
            window.manhwas = [];
            window.render('home-grid', window.manhwas);
            window.render('library-grid', window.manhwas);
            window.updateHeroBanner();
            document.getElementById('main-search').value = "";
        };
    </script>
</body>
</html>
