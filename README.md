import React, { useState } from 'react';
import { 
  Home, BookOpen, Upload, User, Search, Heart, 
  Bookmark, ChevronLeft, ChevronRight, Star, Plus, 
  Image as ImageIcon, Sparkles, LogIn, LogOut, Play, 
  List, Menu, X
} from 'lucide-react';

// --- DATOS DE PRUEBA (MOCKS) ---
const MOCK_MANGAS = [
  {
    id: 1,
    title: 'Cazador de Sombras',
    author: 'Studio Zero',
    genre: 'Acción',
    status: 'En Emisión',
    cover: 'https://images.unsplash.com/photo-1618336753974-aae8e04500a6?auto=format&fit=crop&w=600&q=80',
    banner: 'https://images.unsplash.com/photo-1578632767115-351597cf2477?auto=format&fit=crop&w=1200&q=80',
    synopsis: 'En una ciudad dividida por clanes oscuros, un joven despierta con el poder de absorber las sombras. Ahora debe decidir si usar este don para salvar a su hermana o hundir al mundo en la oscuridad.',
    likes: 1240,
    chapters: [
      { id: 101, number: 1, title: 'El Despertar', date: 'Hace 2 días' },
      { id: 102, number: 2, title: 'Sombras en la Capital', date: 'Hace 1 día' }
    ]
  },
  {
    id: 2,
    title: 'Reino Celestial',
    author: 'Aria M.',
    genre: 'Fantasía',
    status: 'Terminado',
    cover: 'https://images.unsplash.com/photo-1518709268805-4e9042af9f23?auto=format&fit=crop&w=600&q=80',
    banner: 'https://images.unsplash.com/photo-1506466010722-395aa2bef877?auto=format&fit=crop&w=1200&q=80',
    synopsis: 'Una guerrera exiliada descubre que los dioses no han abandonado su reino, sino que están atrapados en cristales elementales esparcidos por el mundo.',
    likes: 850,
    chapters: [
      { id: 201, number: 1, title: 'Prólogo', date: 'Hace 1 mes' },
      { id: 202, number: 2, title: 'El Cristal de Fuego', date: 'Hace 3 semanas' }
    ]
  },
  {
    id: 3,
    title: 'Ecos del Mañana',
    author: 'Kenji D.',
    genre: 'Ciencia Ficción',
    status: 'En Emisión',
    cover: 'https://images.unsplash.com/photo-1514525253161-7a46d19cd819?auto=format&fit=crop&w=600&q=80',
    synopsis: 'En el año 2140, los recuerdos pueden ser extraídos y vendidos. Un detective cibernético investiga un asesinato donde a la víctima le robaron su último día de memoria.',
    likes: 300,
    chapters: [
      { id: 301, number: 1, title: 'Memoria Borrada', date: 'Hace 5 horas' }
    ]
  },
  {
    id: 4,
    title: 'Escuela de Magia Zero',
    author: 'Luz & Sombra',
    genre: 'Comedia',
    status: 'En Pausa',
    cover: 'https://images.unsplash.com/photo-1541562232579-512a21360020?auto=format&fit=crop&w=600&q=80',
    synopsis: 'El peor estudiante de la academia más prestigiosa resulta tener una afinidad mágica que nadie ha visto en mil años: la capacidad de anular los hechizos ajenos.',
    likes: 2100,
    chapters: [
      { id: 401, number: 1, title: 'El Peor del Salón', date: 'Hace 2 meses' }
    ]
  }
];

// --- COMPONENTE PRINCIPAL ---
export default function App() {
  const [currentView, setCurrentView] = useState('home'); // home, library, publish, profile, detail, reader
  const [selectedManga, setSelectedManga] = useState(null);
  const [selectedChapter, setSelectedChapter] = useState(null);
  const [user, setUser] = useState(null); // null = invitado
  const [showAuthModal, setShowAuthModal] = useState(false);
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

  // Navegación
  const navigate = (view, data = null) => {
    setCurrentView(view);
    setMobileMenuOpen(false);
    if (view === 'detail' && data) setSelectedManga(data);
    if (view === 'reader' && data) setSelectedChapter(data);
    window.scrollTo(0, 0);
  };

  const handleLogin = (e) => {
    e.preventDefault();
    setUser({ name: 'Usuario Anilore', role: 'Lector' });
    setShowAuthModal(false);
  };

  const handleLogout = () => {
    setUser(null);
    navigate('home');
  };

  // --- COMPONENTES DE VISTA ---

  const Navbar = () => (
    <nav className="sticky top-0 z-50 bg-slate-950/80 backdrop-blur-md border-b border-slate-800">
      <div className="max-w-7xl mx-auto px-4">
        <div className="flex items-center justify-between h-16">
          {/* Logo */}
          <div 
            className="flex items-center gap-2 cursor-pointer"
            onClick={() => navigate('home')}
          >
            <div className="w-8 h-8 rounded-lg bg-indigo-600 flex items-center justify-center font-bold text-white shadow-lg shadow-indigo-600/20">
              A
            </div>
            <span className="text-xl font-bold bg-gradient-to-r from-white to-slate-400 bg-clip-text text-transparent">
              Anilore
            </span>
          </div>

          {/* Desktop Nav */}
          <div className="hidden md:flex items-center gap-6">
            <NavItem icon={<Home size={18} />} label="Inicio" view="home" />
            <NavItem icon={<BookOpen size={18} />} label="Biblioteca" view="library" />
            <NavItem icon={<Upload size={18} />} label="Publicar" view="publish" />
            {user && <NavItem icon={<User size={18} />} label="Mi Perfil" view="profile" />}
          </div>

          {/* Auth / Mobile Toggle */}
          <div className="flex items-center gap-4">
            <div className="hidden md:block">
              {user ? (
                <button onClick={handleLogout} className="text-slate-400 hover:text-white flex items-center gap-2 text-sm transition-colors">
                  <LogOut size={18} /> Salir
                </button>
              ) : (
                <button 
                  onClick={() => setShowAuthModal(true)}
                  className="bg-indigo-600 hover:bg-indigo-500 text-white px-4 py-2 rounded-lg text-sm font-medium transition-all shadow-lg shadow-indigo-600/20 flex items-center gap-2"
                >
                  <LogIn size={18} /> Iniciar Sesión
                </button>
              )}
            </div>
            <button 
              className="md:hidden text-slate-300"
              onClick={() => setMobileMenuOpen(!mobileMenuOpen)}
            >
              {mobileMenuOpen ? <X size={24} /> : <Menu size={24} />}
            </button>
          </div>
        </div>
      </div>

      {/* Mobile Menu */}
      {mobileMenuOpen && (
        <div className="md:hidden bg-slate-900 border-b border-slate-800 px-4 py-4 space-y-4">
          <MobileNavItem icon={<Home size={20} />} label="Inicio" view="home" />
          <MobileNavItem icon={<BookOpen size={20} />} label="Biblioteca" view="library" />
          <MobileNavItem icon={<Upload size={20} />} label="Publicar" view="publish" />
          {user ? (
             <>
               <MobileNavItem icon={<User size={20} />} label="Mi Perfil" view="profile" />
               <button onClick={handleLogout} className="w-full text-left px-4 py-3 text-red-400 flex items-center gap-3 rounded-lg hover:bg-slate-800">
                  <LogOut size={20} /> Cerrar Sesión
               </button>
             </>
          ) : (
             <button 
               onClick={() => { setShowAuthModal(true); setMobileMenuOpen(false); }}
               className="w-full bg-indigo-600 text-white px-4 py-3 rounded-lg font-medium flex items-center justify-center gap-2"
             >
               <LogIn size={20} /> Iniciar Sesión
             </button>
          )}
        </div>
      )}
    </nav>
  );

  const NavItem = ({ icon, label, view }) => {
    const isActive = currentView === view;
    return (
      <button 
        onClick={() => navigate(view)}
        className={`flex items-center gap-2 text-sm font-medium transition-colors ${isActive ? 'text-indigo-400' : 'text-slate-400 hover:text-white'}`}
      >
        {icon} {label}
      </button>
    );
  };

  const MobileNavItem = ({ icon, label, view }) => {
    const isActive = currentView === view;
    return (
      <button 
        onClick={() => navigate(view)}
        className={`w-full flex items-center gap-3 px-4 py-3 rounded-lg font-medium transition-colors ${isActive ? 'bg-indigo-600/10 text-indigo-400' : 'text-slate-300 hover:bg-slate-800'}`}
      >
        {icon} {label}
      </button>
    );
  };

  const HomeView = () => (
    <div className="space-y-12 pb-12">
      {/* Hero Section */}
      <section className="relative h-[60vh] min-h-[400px] flex items-end pb-12">
        <div className="absolute inset-0">
          <img 
            src={MOCK_MANGAS[0].banner} 
            alt="Hero Banner" 
            className="w-full h-full object-cover opacity-40"
          />
          <div className="absolute inset-0 bg-gradient-to-t from-slate-950 via-slate-950/60 to-transparent" />
        </div>
        <div className="relative z-10 max-w-7xl mx-auto px-4 w-full">
          <span className="inline-block px-3 py-1 bg-indigo-600 text-white text-xs font-bold rounded-full mb-4 uppercase tracking-wider">
            Recomendado
          </span>
          <h1 className="text-4xl md:text-6xl font-extrabold text-white mb-4 max-w-2xl leading-tight">
            {MOCK_MANGAS[0].title}
          </h1>
          <p className="text-slate-300 max-w-xl text-lg mb-8 line-clamp-2 md:line-clamp-3">
            {MOCK_MANGAS[0].synopsis}
          </p>
          <div className="flex gap-4">
            <button 
              onClick={() => navigate('detail', MOCK_MANGAS[0])}
              className="bg-white text-slate-950 px-6 py-3 rounded-xl font-bold flex items-center gap-2 hover:bg-slate-200 transition-all"
            >
              <BookOpen size={20} /> Leer Ahora
            </button>
            <button className="bg-slate-800/50 hover:bg-slate-800 text-white backdrop-blur-md px-6 py-3 rounded-xl font-bold flex items-center gap-2 transition-all">
              <Plus size={20} /> Mi Lista
            </button>
          </div>
        </div>
      </section>

      {/* Últimas Novedades */}
      <section className="max-w-7xl mx-auto px-4">
        <div className="flex items-center justify-between mb-6">
          <h2 className="text-2xl font-bold text-white flex items-center gap-2">
            <Sparkles className="text-indigo-400" /> Últimas Novedades
          </h2>
          <button 
            onClick={() => navigate('library')}
            className="text-indigo-400 text-sm font-medium hover:text-indigo-300 flex items-center gap-1"
          >
            Ver todo <ChevronRight size={16} />
          </button>
        </div>
        <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4 md:gap-6">
          {MOCK_MANGAS.map(manga => (
            <MangaCard key={manga.id} manga={manga} />
          ))}
        </div>
      </section>
    </div>
  );

  const LibraryView = () => (
    <div className="max-w-7xl mx-auto px-4 py-8">
      <div className="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-8">
        <h1 className="text-3xl font-bold text-white">Biblioteca Completa</h1>
        
        {/* Filtros Básicos */}
        <div className="flex items-center gap-2 bg-slate-900 p-1 rounded-lg border border-slate-800 overflow-x-auto">
          {['Todos', 'Acción', 'Fantasía', 'Comedia', 'Ciencia Ficción'].map((genre, idx) => (
            <button 
              key={idx}
              className={`px-4 py-2 rounded-md text-sm font-medium whitespace-nowrap transition-colors ${idx === 0 ? 'bg-indigo-600 text-white' : 'text-slate-400 hover:text-white hover:bg-slate-800'}`}
            >
              {genre}
            </button>
          ))}
        </div>
      </div>

      <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4 md:gap-6">
        {MOCK_MANGAS.map(manga => (
          <MangaCard key={manga.id} manga={manga} />
        ))}
        {/* Duplicamos para rellenar la grilla de demo */}
        {MOCK_MANGAS.map(manga => (
          <MangaCard key={manga.id + '_dup'} manga={{...manga, id: manga.id+100}} />
        ))}
      </div>
    </div>
  );

  const MangaCard = ({ manga }) => (
    <div 
      className="group relative flex flex-col gap-2 cursor-pointer"
      onClick={() => navigate('detail', manga)}
    >
      <div className="relative aspect-[2/3] overflow-hidden rounded-xl bg-slate-800 shadow-lg">
        <img 
          src={manga.cover} 
          alt={manga.title} 
          className="w-full h-full object-cover transition-transform duration-500 group-hover:scale-110"
        />
        <div className="absolute inset-0 bg-gradient-to-t from-slate-950 via-slate-950/20 to-transparent opacity-80" />
        
        {/* Hover Overlay */}
        <div className="absolute inset-0 bg-indigo-600/20 opacity-0 group-hover:opacity-100 transition-opacity flex items-center justify-center backdrop-blur-[2px]">
          <div className="bg-slate-950/80 text-white px-4 py-2 rounded-full font-bold flex items-center gap-2 transform translate-y-4 group-hover:translate-y-0 transition-all">
            <Play size={16} fill="currentColor" /> Leer
          </div>
        </div>

        {/* Status / Genre Tags */}
        <div className="absolute top-2 left-2 right-2 flex justify-between items-start">
          <span className="bg-slate-950/80 backdrop-blur text-xs font-bold px-2 py-1 rounded text-white">
            {manga.genre}
          </span>
          <button className="bg-slate-950/50 hover:bg-slate-950 text-slate-300 hover:text-indigo-400 p-1.5 rounded-full transition-colors backdrop-blur">
            <Bookmark size={16} />
          </button>
        </div>
      </div>
      <div>
        <h3 className="font-bold text-white text-base leading-tight group-hover:text-indigo-400 transition-colors line-clamp-2">
          {manga.title}
        </h3>
        <p className="text-slate-400 text-sm mt-1">{manga.author}</p>
      </div>
    </div>
  );

  const MangaDetailView = () => {
    if (!selectedManga) return null;
    return (
      <div className="pb-12">
        {/* Banner */}
        <div className="h-64 md:h-80 relative w-full bg-slate-900">
          <img 
            src={selectedManga.banner || selectedManga.cover} 
            alt="Banner" 
            className="w-full h-full object-cover opacity-30 blur-sm"
          />
          <div className="absolute inset-0 bg-gradient-to-t from-slate-950 to-transparent" />
        </div>

        <div className="max-w-5xl mx-auto px-4 relative -mt-32">
          <div className="flex flex-col md:flex-row gap-8">
            {/* Cover Column */}
            <div className="flex-shrink-0 w-48 md:w-64 mx-auto md:mx-0">
              <img 
                src={selectedManga.cover} 
                alt={selectedManga.title} 
                className="w-full aspect-[2/3] object-cover rounded-xl shadow-2xl shadow-black ring-4 ring-slate-950"
              />
              <button 
                onClick={() => navigate('reader', { manga: selectedManga, chapter: selectedManga.chapters[0] })}
                className="w-full mt-6 bg-indigo-600 hover:bg-indigo-500 text-white py-3 rounded-xl font-bold flex items-center justify-center gap-2 transition-colors shadow-lg shadow-indigo-600/20"
              >
                <Play size={20} fill="currentColor" /> Comenzar a Leer
              </button>
            </div>

            {/* Info Column */}
            <div className="flex-1 pt-4 md:pt-32">
              <div className="flex flex-wrap items-center gap-3 mb-2">
                <span className="bg-indigo-500/20 text-indigo-400 border border-indigo-500/30 px-3 py-1 rounded-full text-xs font-bold uppercase">
                  {selectedManga.genre}
                </span>
                <span className={`px-3 py-1 rounded-full text-xs font-bold uppercase ${selectedManga.status === 'En Emisión' ? 'bg-green-500/20 text-green-400 border border-green-500/30' : 'bg-slate-800 text-slate-300'}`}>
                  {selectedManga.status}
                </span>
              </div>
              <h1 className="text-4xl md:text-5xl font-extrabold text-white mb-2">{selectedManga.title}</h1>
              <p className="text-xl text-slate-400 mb-6">{selectedManga.author}</p>
              
              <div className="flex items-center gap-6 mb-8 text-slate-300 bg-slate-900/50 p-4 rounded-xl border border-slate-800">
                <div className="flex items-center gap-2">
                  <Heart className="text-pink-500" fill="currentColor" size={20} />
                  <span className="font-bold">{selectedManga.likes}</span>
                </div>
                <div className="w-px h-6 bg-slate-700" />
                <button className="flex items-center gap-2 hover:text-white transition-colors">
                  <Bookmark size={20} /> Guardar
                </button>
              </div>

              <h3 className="text-lg font-bold text-white mb-2">Sinopsis</h3>
              <p className="text-slate-300 leading-relaxed mb-10">
                {selectedManga.synopsis}
              </p>

              {/* Chapters List */}
              <div>
                <div className="flex items-center justify-between mb-4 border-b border-slate-800 pb-2">
                  <h3 className="text-xl font-bold text-white flex items-center gap-2">
                    <List size={20} /> Capítulos ({selectedManga.chapters.length})
                  </h3>
                </div>
                <div className="space-y-3">
                  {selectedManga.chapters.map(chapter => (
                    <div 
                      key={chapter.id}
                      onClick={() => navigate('reader', { manga: selectedManga, chapter })}
                      className="group bg-slate-900/50 hover:bg-slate-800 border border-slate-800 hover:border-slate-700 p-4 rounded-xl flex items-center justify-between cursor-pointer transition-all"
                    >
                      <div>
                        <h4 className="font-bold text-white group-hover:text-indigo-400 transition-colors">
                          Capítulo {chapter.number}: {chapter.title}
                        </h4>
                        <span className="text-xs text-slate-500">{chapter.date}</span>
                      </div>
                      <div className="w-10 h-10 rounded-full bg-slate-950 flex items-center justify-center text-slate-400 group-hover:bg-indigo-600 group-hover:text-white transition-colors">
                        <Play size={16} fill="currentColor" className="ml-1" />
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    );
  };

  const ReaderView = () => {
    if (!selectedChapter) return null;
    return (
      <div className="min-h-screen bg-black">
        {/* Reader Nav */}
        <div className="sticky top-0 z-40 bg-slate-950/90 backdrop-blur p-4 border-b border-slate-800 flex items-center justify-between">
          <button 
            onClick={() => navigate('detail', selectedManga)}
            className="text-slate-400 hover:text-white flex items-center gap-2"
          >
            <ChevronLeft size={20} /> Volver
          </button>
          <div className="text-center">
            <h2 className="text-white font-bold">{selectedManga?.title}</h2>
            <p className="text-slate-400 text-xs">Capítulo {selectedChapter.number}: {selectedChapter.title}</p>
          </div>
          <div className="w-20" /> {/* Spacer */}
        </div>

        {/* Reader Content (Mock images) */}
        <div className="max-w-3xl mx-auto py-8 px-4 flex flex-col gap-4">
          <div className="aspect-[2/3] bg-slate-900 rounded-lg flex items-center justify-center border border-slate-800 relative overflow-hidden">
            <div className="absolute inset-0 bg-[url('https://images.unsplash.com/photo-1557683316-973673baf926?auto=format&fit=crop&w=800&q=80')] opacity-20 bg-cover bg-center" />
            <span className="text-slate-500 text-2xl font-bold relative z-10">[ Página 1 del Manga ]</span>
          </div>
          <div className="aspect-[2/3] bg-slate-900 rounded-lg flex items-center justify-center border border-slate-800 relative overflow-hidden">
            <div className="absolute inset-0 bg-[url('https://images.unsplash.com/photo-1557682250-33bd709cbe85?auto=format&fit=crop&w=800&q=80')] opacity-20 bg-cover bg-center" />
            <span className="text-slate-500 text-2xl font-bold relative z-10">[ Página 2 del Manga ]</span>
          </div>
        </div>

        {/* Reader Controls */}
        <div className="fixed bottom-0 left-0 right-0 p-4 bg-gradient-to-t from-black to-transparent flex justify-center gap-4">
          <button className="bg-slate-800 hover:bg-slate-700 text-white px-6 py-3 rounded-full font-medium flex items-center gap-2 transition-colors">
            <ChevronLeft size={18} /> Anterior
          </button>
          <button className="bg-indigo-600 hover:bg-indigo-500 text-white px-6 py-3 rounded-full font-medium flex items-center gap-2 transition-colors shadow-lg shadow-indigo-600/20">
            Siguiente <ChevronRight size={18} />
          </button>
        </div>
      </div>
    );
  };

  const PublishView = () => {
    const [tab, setTab] = useState('obra'); // obra, capitulo

    if (!user) {
      return (
        <div className="max-w-2xl mx-auto px-4 py-20 text-center">
          <div className="w-20 h-20 bg-indigo-600/20 rounded-full flex items-center justify-center mx-auto mb-6">
            <Upload size={40} className="text-indigo-400" />
          </div>
          <h2 className="text-2xl font-bold text-white mb-4">Únete a nuestros creadores</h2>
          <p className="text-slate-400 mb-8">Para publicar tus propias obras o capítulos, necesitas iniciar sesión o crear una cuenta en Anilore.</p>
          <button 
            onClick={() => setShowAuthModal(true)}
            className="bg-indigo-600 text-white px-8 py-3 rounded-xl font-bold hover:bg-indigo-500 transition-colors"
          >
            Iniciar Sesión
          </button>
        </div>
      );
    }

    return (
      <div className="max-w-3xl mx-auto px-4 py-8">
        <h1 className="text-3xl font-bold text-white mb-8">Panel de Publicación</h1>
        
        {/* Tabs */}
        <div className="flex p-1 bg-slate-900 rounded-xl mb-8">
          <button 
            onClick={() => setTab('obra')}
            className={`flex-1 py-3 text-sm font-bold rounded-lg transition-all ${tab === 'obra' ? 'bg-slate-800 text-white shadow' : 'text-slate-400 hover:text-white'}`}
          >
            Nueva Obra
          </button>
          <button 
            onClick={() => setTab('capitulo')}
            className={`flex-1 py-3 text-sm font-bold rounded-lg transition-all ${tab === 'capitulo' ? 'bg-slate-800 text-white shadow' : 'text-slate-400 hover:text-white'}`}
          >
            Subir Capítulo
          </button>
        </div>

        <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6 md:p-8">
          {tab === 'obra' ? (
            <form className="space-y-6" onSubmit={e => e.preventDefault()}>
              <div>
                <label className="block text-sm font-medium text-slate-300 mb-2">Título de la Obra</label>
                <input type="text" className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 transition-all" placeholder="Ej. El Viaje del Héroe" />
              </div>
              
              <div>
                <label className="block text-sm font-medium text-slate-300 mb-2">Género Principal</label>
                <select className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-indigo-500 appearance-none">
                  <option>Seleccionar...</option>
                  <option>Acción</option>
                  <option>Fantasía</option>
                  <option>Romance</option>
                  <option>Comedia</option>
                  <option>Drama</option>
                </select>
              </div>

              <div>
                <div className="flex justify-between items-end mb-2">
                  <label className="block text-sm font-medium text-slate-300">Sinopsis</label>
                  <button type="button" className="text-xs text-indigo-400 flex items-center gap-1 hover:text-indigo-300">
                    <Sparkles size={14} /> Generar con IA
                  </button>
                </div>
                <textarea rows="4" className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500 transition-all resize-none" placeholder="Describe de qué trata tu historia..."></textarea>
              </div>

              <div>
                <label className="block text-sm font-medium text-slate-300 mb-2">Portada (Recomendado 600x900px)</label>
                <div className="border-2 border-dashed border-slate-700 hover:border-indigo-500 rounded-xl p-8 text-center transition-colors cursor-pointer group bg-slate-950">
                  <ImageIcon size={32} className="mx-auto mb-3 text-slate-500 group-hover:text-indigo-400 transition-colors" />
                  <p className="text-sm text-slate-400 group-hover:text-slate-300">Haz clic para subir o arrastra la imagen aquí</p>
                </div>
              </div>

              <button type="submit" className="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-4 rounded-xl transition-all shadow-lg shadow-indigo-600/20">
                Publicar Obra
              </button>
            </form>
          ) : (
            <form className="space-y-6" onSubmit={e => e.preventDefault()}>
              <div>
                <label className="block text-sm font-medium text-slate-300 mb-2">Seleccionar Obra</label>
                <select className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-indigo-500 appearance-none">
                  <option>Mis obras...</option>
                  <option>Cazador de Sombras</option>
                </select>
              </div>

              <div className="grid grid-cols-2 gap-4">
                <div>
                  <label className="block text-sm font-medium text-slate-300 mb-2">Número</label>
                  <input type="number" className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-indigo-500" placeholder="Ej. 1" />
                </div>
                <div>
                  <label className="block text-sm font-medium text-slate-300 mb-2">Título del capítulo</label>
                  <input type="text" className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-indigo-500" placeholder="Ej. El inicio" />
                </div>
              </div>

              <div>
                <label className="block text-sm font-medium text-slate-300 mb-2">Páginas del Capítulo</label>
                <div className="border-2 border-dashed border-slate-700 hover:border-indigo-500 rounded-xl p-8 text-center transition-colors cursor-pointer group bg-slate-950">
                  <Upload size={32} className="mx-auto mb-3 text-slate-500 group-hover:text-indigo-400 transition-colors" />
                  <p className="text-sm text-slate-400 mb-1">Selecciona múltiples imágenes (JPG, PNG)</p>
                  <p className="text-xs text-slate-500">Puedes arrastrar y soltar los archivos</p>
                </div>
              </div>

              <button type="submit" className="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-4 rounded-xl transition-all shadow-lg shadow-indigo-600/20">
                Subir Capítulo
              </button>
            </form>
          )}
        </div>
      </div>
    );
  };

  const ProfileView = () => {
    if (!user) {
      navigate('home');
      return null;
    }

    return (
      <div className="max-w-5xl mx-auto px-4 py-8">
        <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6 md:p-8 flex flex-col md:flex-row items-center gap-6 mb-8">
          <div className="w-24 h-24 bg-gradient-to-tr from-indigo-600 to-purple-600 rounded-full p-1">
            <div className="w-full h-full bg-slate-950 rounded-full flex items-center justify-center border-4 border-slate-900">
              <User size={40} className="text-slate-400" />
            </div>
          </div>
          <div className="text-center md:text-left flex-1">
            <h1 className="text-3xl font-bold text-white mb-2">{user.name}</h1>
            <p className="text-slate-400 flex items-center justify-center md:justify-start gap-2">
              <Star size={16} className="text-yellow-500" /> Coleccionista de Anilore
            </p>
          </div>
          <div className="flex gap-4 w-full md:w-auto">
            <div className="bg-slate-950 border border-slate-800 rounded-xl px-6 py-4 flex-1 text-center">
              <p className="text-2xl font-bold text-white">12</p>
              <p className="text-xs text-slate-500 uppercase font-bold tracking-wider">Leídos</p>
            </div>
            <div className="bg-slate-950 border border-slate-800 rounded-xl px-6 py-4 flex-1 text-center">
              <p className="text-2xl font-bold text-white">5</p>
              <p className="text-xs text-slate-500 uppercase font-bold tracking-wider">Guardados</p>
            </div>
          </div>
        </div>

        <h2 className="text-2xl font-bold text-white mb-6">Guardados Recientemente</h2>
        <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4 md:gap-6">
          {MOCK_MANGAS.slice(0,2).map(manga => (
            <MangaCard key={manga.id} manga={manga} />
          ))}
        </div>
      </div>
    );
  };

  const AuthModal = () => {
    if (!showAuthModal) return null;
    return (
      <div className="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/60 backdrop-blur-sm">
        <div className="bg-slate-900 border border-slate-800 rounded-2xl w-full max-w-md overflow-hidden shadow-2xl">
          <div className="p-6 md:p-8">
            <div className="flex justify-between items-center mb-8">
              <h2 className="text-2xl font-bold text-white">Bienvenido</h2>
              <button onClick={() => setShowAuthModal(false)} className="text-slate-500 hover:text-white">
                <X size={24} />
              </button>
            </div>
            <form onSubmit={handleLogin} className="space-y-4">
              <div>
                <label className="block text-sm font-medium text-slate-400 mb-1">Correo Electrónico</label>
                <input type="email" required className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-white focus:border-indigo-500 focus:outline-none" placeholder="correo@ejemplo.com" />
              </div>
              <div>
                <label className="block text-sm font-medium text-slate-400 mb-1">Contraseña</label>
                <input type="password" required className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-white focus:border-indigo-500 focus:outline-none" placeholder="••••••••" />
              </div>
              <button type="submit" className="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-3 rounded-xl mt-4 transition-colors">
                Entrar a Anilore
              </button>
            </form>
            <p className="text-center text-slate-400 text-sm mt-6">
              ¿No tienes cuenta? <a href="#" className="text-indigo-400 font-bold hover:underline">Regístrate aquí</a>
            </p>
          </div>
        </div>
      </div>
    );
  };

  // Renderizador principal
  return (
    <div className="min-h-screen bg-slate-950 font-sans selection:bg-indigo-500/30">
      {currentView !== 'reader' && <Navbar />}
      
      <main className="animate-in fade-in duration-300">
        {currentView === 'home' && <HomeView />}
        {currentView === 'library' && <LibraryView />}
        {currentView === 'detail' && <MangaDetailView />}
        {currentView === 'reader' && <ReaderView />}
        {currentView === 'publish' && <PublishView />}
        {currentView === 'profile' && <ProfileView />}
      </main>

      <AuthModal />
    </div>
  );
}
