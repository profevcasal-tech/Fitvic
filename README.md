import React, { useState, useEffect, useMemo } from 'react';

// --- TEMAS Y PALETAS DE COLOR DE LA APP FITVIC ---
const THEMES = {
  emerald: {
    name: 'Verde Esmeralda (Default)',
    bg: 'bg-slate-900 text-slate-100',
    card: 'bg-slate-800/60 border-slate-700/60',
    accentText: 'text-emerald-400',
    accentBg: 'bg-emerald-500',
    accentBorder: 'border-emerald-500',
    badge: 'bg-emerald-500/10 text-emerald-400 border-emerald-500/20',
    innerCard: 'bg-slate-900/80 border-slate-700/50'
  },
  cyan: {
    name: 'Azul Océano',
    bg: 'bg-slate-950 text-slate-100',
    card: 'bg-slate-900/70 border-cyan-900/50',
    accentText: 'text-cyan-400',
    accentBg: 'bg-cyan-500',
    accentBorder: 'border-cyan-500',
    badge: 'bg-cyan-500/10 text-cyan-400 border-cyan-500/20',
    innerCard: 'bg-slate-950/80 border-cyan-900/40'
  },
  violet: {
    name: 'Morado Neón',
    bg: 'bg-slate-950 text-slate-100',
    card: 'bg-slate-900/80 border-violet-900/50',
    accentText: 'text-violet-400',
    accentBg: 'bg-violet-500',
    accentBorder: 'border-violet-500',
    badge: 'bg-violet-500/10 text-violet-400 border-violet-500/20',
    innerCard: 'bg-slate-900/90 border-violet-900/40'
  },
  light: {
    name: 'Modo Claro',
    bg: 'bg-gray-100 text-gray-900',
    card: 'bg-white border-gray-200 shadow-sm',
    accentText: 'text-emerald-600',
    accentBg: 'bg-emerald-600',
    accentBorder: 'border-emerald-600',
    badge: 'bg-emerald-100 text-emerald-700 border-emerald-200',
    innerCard: 'bg-gray-50 border-gray-200'
  }
};

// --- BASE DE DATOS DE EJERCICIOS CON ADAPTACIONES Y ANIMACIONES ---
const EXERCISE_DATABASE = [
  // Empuje Cuádriceps / Pierna
  {
    id: 1,
    name: 'Sentadilla Trasera con Barra',
    category: 'Pierna',
    pattern: 'Cuádriceps',
    riskTags: ['lumbar', 'knee'],
    safeAlternative: 'Prensa de Piernas a 45°',
    setsReps: '3 x 8-10',
    animationType: 'squat',
    steps: [
      'Pies al ancho de hombros con las puntas ligeramente hacia afuera (15°).',
      'Inspirar profundo, activar el core y mantener la espalda neutra.',
      'Bajar de manera controlada flexionando rodillas y cadera hasta los 90°.'
    ],
    tip: 'Mantené los talones firmes contra el suelo en todo momento.'
  },
  {
    id: 2,
    name: 'Prensa de Piernas a 45°',
    category: 'Pierna',
    pattern: 'Cuádriceps',
    riskTags: [],
    safeAlternative: null,
    setsReps: '4 x 10-12',
    animationType: 'press',
    steps: [
      'Apoyar la espalda por completo en el respaldo de la máquina.',
      'Colocar los pies al centro de la plataforma a la anchura de caderas.',
      'Bajar de forma guiada sin desatarse la cadera del respaldo.'
    ],
    tip: 'Evitá bloquear las rodillas al extender en la parte superior.'
  },
  {
    id: 3,
    name: 'Sentadilla a Cajón 90°',
    category: 'Pierna',
    pattern: 'Cuádriceps',
    riskTags: ['lumbar'],
    safeAlternative: 'Sillón de Extensión de Cuádriceps',
    setsReps: '3 x 10-12',
    animationType: 'boxsquat',
    steps: [
      'Ubicarse delante de un cajón o banco firme.',
      'Bajar haciendo contacto suave con el banco sin perder tensión.',
      'Empujar con los talones para volver a la posición inicial.'
    ],
    tip: 'Ideal para limitar el rango de movimiento y cuidar la rodilla.'
  },
  {
    id: 4,
    name: 'Sillón de Extensión de Cuádriceps',
    category: 'Pierna',
    pattern: 'Cuádriceps',
    riskTags: [],
    safeAlternative: null,
    setsReps: '3 x 12-15',
    animationType: 'legextension',
    steps: [
      'Ajustar el rodillo sobre el empeine y la espalda apoyada.',
      'Extender ambas piernas de forma fluida sosteniendo 1 segundo arriba.',
      'Descender en 2 segundos de forma controlada.'
    ],
    tip: 'Excelente aislamiento sin carga sobre la columna vertebral.'
  },

  // Cadena Posterior / Cadera
  {
    id: 5,
    name: 'Peso Muerto Rumano',
    category: 'Pierna',
    pattern: 'Isquios/Glúteo',
    riskTags: ['lumbar'],
    safeAlternative: 'Curl Femoral Tumbado en Máquina',
    setsReps: '3 x 8-10',
    animationType: 'rdl',
    steps: [
      'Sostener mancuernas o barra a la altura de la cadera.',
      'Llevar la cadera hacia atrás manteniendo las rodillas semi-flexionadas.',
      'Bajar el peso rozando los muslos hasta sentir estiramiento en isquios.'
    ],
    tip: 'Mantener la columna completamente alineada sin redondear.'
  },
  {
    id: 6,
    name: 'Hip Thrust (Empuje de Cadera)',
    category: 'Pierna',
    pattern: 'Isquios/Glúteo',
    riskTags: [],
    safeAlternative: null,
    setsReps: '4 x 10-12',
    animationType: 'hipthrust',
    steps: [
      'Apoyar el borde inferior de las escápulas sobre un banco.',
      'Empujar fuertemente con los talones elevando la cadera.',
      'Apretar glúteos arriba formando una línea recta entre tronco y muslos.'
    ],
    tip: 'Mantené la barbilla pegada al Pecho para no sobrecargar el cuello.'
  },
  {
    id: 7,
    name: 'Curl Femoral Sentado',
    category: 'Pierna',
    pattern: 'Isquios/Glúteo',
    riskTags: [],
    safeAlternative: null,
    setsReps: '3 x 12-15',
    animationType: 'legcurl',
    steps: [
      'Asegurar el rodillo superior sobre los muslos para evitar desplazamientos.',
      'Flexionar las rodillas llevando los talones hacia atrás.',
      'Regresar despacio al punto de inicio.'
    ],
    tip: 'Cero impacto lumbar, ideal para hipertrofia de isquiotibiales.'
  },

  // Pecho / Empuje Horizontal
  {
    id: 9,
    name: 'Press de Banca Plano con Barra',
    category: 'Empuje',
    pattern: 'Pecho',
    riskTags: ['shoulder', 'hypertension'],
    safeAlternative: 'Press Inclinado con Mancuernas',
    setsReps: '4 x 8-10',
    animationType: 'benchpress',
    steps: [
      'Cinco puntos de contacto: pies firmes, cadera, escápulas y cabeza.',
      'Bajar la barra al esternón manteniendo codos a 45° respecto al cuerpo.',
      'Empujar hacia arriba expulsando el aire.'
    ],
    tip: 'Retraé las escápulas antes de desenganchar la barra.'
  },
  {
    id: 10,
    name: 'Press Inclinado con Mancuernas',
    category: 'Empuje',
    pattern: 'Pecho',
    riskTags: ['shoulder'],
    safeAlternative: 'Press de Pecho en Máquina Articulada',
    setsReps: '3 x 10-12',
    animationType: 'inclinepress',
    steps: [
      'Ajustar el banco a 30° de inclinación.',
      'Bajar las mancuernas hacia los lados del Pecho.',
      'Empujar en diagonal hacia el techo hasta juntar levemente los pesos.'
    ],
    tip: 'El agarre semineutro cuida los articulaciones del hombro.'
  },
  {
    id: 11,
    name: 'Press de Pecho en Máquina Articulada',
    category: 'Empuje',
    pattern: 'Pecho',
    riskTags: [],
    safeAlternative: null,
    setsReps: '3 x 12',
    animationType: 'chestmachine',
    steps: [
      'Ajustar la altura del asiento para que los agarres queden al nivel del Pecho.',
      'Empujar hacia adelante de forma constante.',
      'Controlar el regreso sin dejar que los pesos choquen.'
    ],
    tip: 'Permite trabajar cerca del fallo muscular con máxima seguridad.'
  },

  // Hombro / Empuje Vertical
  {
    id: 13,
    name: 'Press Militar con Barra',
    category: 'Empuje',
    pattern: 'Hombro',
    riskTags: ['shoulder', 'lumbar', 'hypertension'],
    safeAlternative: 'Press Landmine',
    setsReps: '3 x 8-10',
    animationType: 'overheadpress',
    steps: [
      'Sostener la barra a la altura de las clavículas.',
      'Empujar hacia arriba pasando la barra por delante del rostro.',
      'Bloquear arriba alineando la barra con la coronilla.'
    ],
    tip: 'Apretar glúteos y abdomen para evitar arquear la columna.'
  },
  {
    id: 14,
    name: 'Press Landmine con Barra',
    category: 'Empuje',
    pattern: 'Hombro',
    riskTags: [],
    safeAlternative: null,
    setsReps: '3 x 10-12',
    animationType: 'landmine',
    steps: [
      'Sostener el extremo de la barra apoyada en la esquina o pivote.',
      'Empujar hacia adelante y arriba en ángulo diagonal.',
      'Bajar con control hacia el Pecho.'
    ],
    tip: 'Ángulo biomecánico natural que respeta el manguito rotador.'
  },
  {
    id: 15,
    name: 'Elevaciones Laterales en Plano Escapular',
    category: 'Empuje',
    pattern: 'Hombro',
    riskTags: [],
    safeAlternative: null,
    setsReps: '4 x 12-15',
    animationType: 'lateralraise',
    steps: [
      'Elevar las mancuernas 30° hacia adelante respecto al plano del cuerpo.',
      'Subir hasta la altura de los hombros con codos ligeramente flexionados.',
      'Bajar lentamente.'
    ],
    tip: 'No subir más allá de la línea de los hombros.'
  },

  // Espalda / Tracción
  {
    id: 17,
    name: 'Remo con Barra de Pie',
    category: 'Tracción',
    pattern: 'Espalda',
    riskTags: ['lumbar'],
    safeAlternative: 'Remo en Banco Inclinado (Pecho Apoyado)',
    setsReps: '4 x 8-10',
    animationType: 'barbellrow',
    steps: [
      'Inclinarse a 45° manteniendo la columna perfectamente recta.',
      'Traccionar la barra hacia el ombligo llevando los codos atrás.',
      'Bajar estirando dorsales.'
    ],
    tip: 'Requiere gran estabilidad lumbar. Si molesta, usar la alternativa apoyada.'
  },
  {
    id: 18,
    name: 'Remo en Banco Inclinado (Pecho Apoyado)',
    category: 'Tracción',
    pattern: 'Espalda',
    riskTags: [],
    safeAlternative: null,
    setsReps: '3 x 10-12',
    animationType: 'chestsupportedrow',
    steps: [
      'Recostarse de Pecho sobre un banco inclinado a 30° o 45°.',
      'Remar las mancuernas hacia los lados de la cadera.',
      'Apretar las escápulas arriba 1 segundo.'
    ],
    tip: 'Elimina 100% de tensión en la zona lumbar.'
  },
  {
    id: 19,
    name: 'Jalón al Pecho (Agarre Neutro)',
    category: 'Tracción',
    pattern: 'Espalda',
    riskTags: [],
    safeAlternative: null,
    setsReps: '4 x 10-12',
    animationType: 'latpulldown',
    steps: [
      'Sujetar el agarre neutro a la altura de los hombros.',
      'Tirar de la polea hacia el Pecho sacando el tórax.',
      'Regresar extendiendo dorsales bajo control.'
    ],
    tip: 'Evitá dar impulsos con el tronco hacia atrás.'
  },

  // Core & Cardio
  {
    id: 23,
    name: 'Pallof Press en Polea',
    category: 'Core',
    pattern: 'Core Estabilidad',
    riskTags: [],
    safeAlternative: null,
    setsReps: '3 x 12 por lado',
    animationType: 'pallof',
    steps: [
      'Ubicarse de lado a la polea a la altura del Pecho.',
      'Sostener el agarre con ambas manos y extender los brazos al frente.',
      'Resistir la fuerza de rotación sin mover el tronco.'
    ],
    tip: 'Estabilidad de core segura para hipertensos y cuidar la zona lumbar.'
  },
  {
    id: 25,
    name: 'Caminata en Cinta con Inclinación (LISS)',
    category: 'Cardio',
    pattern: 'Aeróbico',
    riskTags: [],
    safeAlternative: null,
    setsReps: '20-30 min a paso firme',
    animationType: 'cardio',
    steps: [
      'Ajustar la inclinación entre 5% y 10% a una velocidad de 4.5 a 5.5 km/h.',
      'Manejar un ritmo respiratorio cómodo que permita hablar.',
      'Apoyar el pie de talón a punta.'
    ],
    tip: 'Excelente quema de grasa sin impacto articular.'
  }
];

const INITIAL_FORM_DATA = {
  gender: 'female',
  weight: 68,
  height: 165,
  bodyFat: 26,
  goal: 'recomp',
  daysPerWeek: 4,
  theme: 'emerald',
  conditions: {
    lumbar: false,
    shoulder: false,
    knee: false,
    hypertension: false
  }
};

export default function BodyAnalysisApp() {
  // PERSISTENCIA EN LOCALSTORAGE
  const [formData, setFormData] = useState(() => {
    const saved = localStorage.getItem('fitvic_user_data');
    return saved ? JSON.parse(saved) : INITIAL_FORM_DATA;
  });

  const [completedSessions, setCompletedSessions] = useState(() => {
    const saved = localStorage.getItem('fitvic_weekly_progress');
    return saved ? JSON.parse(saved) : [];
  });

  const [selectedExerciseModal, setSelectedExerciseModal] = useState(null);

  useEffect(() => {
    localStorage.setItem('fitvic_user_data', JSON.stringify(formData));
  }, [formData]);

  useEffect(() => {
    localStorage.setItem('fitvic_weekly_progress', JSON.stringify(completedSessions));
  }, [completedSessions]);

  const theme = THEMES[formData.theme] || THEMES.emerald;

  // CÁLCULOS BIOMÉTRICOS
  const metrics = useMemo(() => {
    const weightKg = parseFloat(formData.weight) || 0;
    const heightM = (parseFloat(formData.height) || 100) / 100;
    const fatPct = (parseFloat(formData.bodyFat) || 0) / 100;

    const bmi = (weightKg / (heightM * heightM)).toFixed(1);
    const fatMassKg = (weightKg * fatPct).toFixed(1);
    const leanMassKg = (weightKg - fatMassKg).toFixed(1);
    const ffmi = (leanMassKg / (heightM * heightM)).toFixed(1);

    return { bmi, fatMassKg, leanMassKg, ffmi };
  }, [formData.weight, formData.height, formData.bodyFat]);

  // HANDLERS
  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const handleCheckboxChange = (key) => {
    setFormData(prev => ({
      ...prev,
      conditions: { ...prev.conditions, [key]: !prev.conditions[key] }
    }));
  };

  const toggleSessionCompleted = (dayNum) => {
    setCompletedSessions(prev =>
      prev.includes(dayNum) ? prev.filter(d => d !== dayNum) : [...prev, dayNum]
    );
  };

  const resetProgress = () => {
    if (window.confirm('¿Reiniciar los entrenamientos marcados de esta semana?')) {
      setCompletedSessions([]);
    }
  };

  const handlePrint = () => {
    window.print();
  };

  // MOTOR DE ADAPTACIÓN DE EJERCICIOS POR SALUD
  const activeConditionsKeys = Object.keys(formData.conditions).filter(k => formData.conditions[k]);

  const processedExercises = useMemo(() => {
    return EXERCISE_DATABASE.map(ex => {
      const hasConflict = ex.riskTags.some(tag => formData.conditions[tag]);
      if (hasConflict) {
        return {
          ...ex,
          isAdapted: true,
          originalName: ex.name,
          name: ex.safeAlternative || 'Ejercicio Adaptado de Seguridad'
        };
      }
      return { ...ex, isAdapted: false };
    });
  }, [formData.conditions]);

  const groupedExercises = useMemo(() => {
    const groups = { Empuje: [], Tracción: [], Pierna: [], Core: [], Cardio: [] };
    processedExercises.forEach(ex => {
      if (groups[ex.category]) groups[ex.category].push(ex);
    });
    return groups;
  }, [processedExercises]);

  return (
    <div className={`min-h-screen ${theme.bg} p-4 md:p-8 font-sans transition-colors duration-300 print:bg-white print:text-black print:p-0`}>
      
      {/* ESTILOS PARA IMPRESIÓN / PDF */}
      <style>{`
        @media print {
          .no-print { display: none !important; }
          body { background: white !important; color: black !important; }
          .print-card { background: #f8fafc !important; border: 1px solid #cbd5e1 !important; color: black !important; page-break-inside: avoid; }
        }
      `}</style>

      {/* HEADER FITVIC */}
      <header className="max-w-7xl mx-auto mb-8 pb-4 border-b border-slate-800 flex flex-wrap justify-between items-center gap-4 print:border-black">
        <div className="flex items-center space-x-3">
          <img 
            src="/logo.png" 
            alt="Fitvic Logo" 
            onError={(e) => { e.target.style.display = 'none'; }}
            className="w-10 h-10 object-contain rounded-xl shadow-md border border-slate-700/50 no-print"
          />
          <div>
            <h1 className={`text-2xl font-bold ${theme.accentText} tracking-wide print:text-black`}>Fitvic</h1>
            <p className="text-xs text-slate-400 print:text-gray-600">Personal Trainer & Análisis Corporal Inteligente</p>
          </div>
        </div>

        <div className="flex items-center space-x-3 no-print">
          {/* Selector de Color/Tema */}
          <div className="flex items-center space-x-1 bg-slate-800/80 p-1.5 rounded-xl border border-slate-700/60">
            {Object.keys(THEMES).map(tKey => (
              <button
                key={tKey}
                onClick={() => setFormData(prev => ({ ...prev, theme: tKey }))}
                className={`px-2.5 py-1 rounded-lg text-xs font-semibold transition-all cursor-pointer ${
                  formData.theme === tKey
                    ? `${THEMES[tKey].accentBg} text-slate-950 shadow-md`
                    : 'text-slate-400 hover:text-white'
                }`}
              >
                {THEMES[tKey].name.split(' ')[0]}
              </button>
            ))}
          </div>

          <button
            onClick={handlePrint}
            className={`${theme.accentBg} text-slate-950 font-bold text-xs px-3.5 py-2 rounded-xl transition-all shadow-md cursor-pointer`}
          >
            📄 PDF / Imprimir
          </button>
        </div>
      </header>

      <main className="max-w-7xl mx-auto grid grid-cols-1 lg:grid-cols-12 gap-8">
        
        {/* COLUMNA IZQUIERDA: CONFIGURACIÓN & PROGRESO */}
        <section className="lg:col-span-4 space-y-6">
          <div className={`${theme.card} rounded-2xl p-6 space-y-6 no-print`}>
            <h2 className={`text-lg font-semibold ${theme.accentText} border-b border-slate-700/50 pb-2`}>
              1. Perfil y Salud
            </h2>

            <div className="grid grid-cols-2 gap-4">
              <div>
                <label className="block text-xs text-slate-400 mb-1">Peso (kg)</label>
                <input
                  type="number"
                  name="weight"
                  value={formData.weight}
                  onChange={handleInputChange}
                  className="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-white"
                />
              </div>
              <div>
                <label className="block text-xs text-slate-400 mb-1">Altura (cm)</label>
                <input
                  type="number"
                  name="height"
                  value={formData.height}
                  onChange={handleInputChange}
                  className="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-white"
                />
              </div>
              <div>
                <label className="block text-xs text-slate-400 mb-1">% Grasa Est.</label>
                <input
                  type="number"
                  name="bodyFat"
                  value={formData.bodyFat}
                  onChange={handleInputChange}
                  className="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-white"
                />
              </div>
              <div>
                <label className="block text-xs text-slate-400 mb-1">Días x Semana</label>
                <select
                  name="daysPerWeek"
                  value={formData.daysPerWeek}
                  onChange={handleInputChange}
                  className="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-white"
                >
                  <option value={2}>2 días</option>
                  <option value={3}>3 días</option>
                  <option value={4}>4 días</option>
                  <option value={5}>5 días</option>
                </select>
              </div>
            </div>

            <div>
              <label className="block text-xs text-slate-400 mb-1">Objetivo</label>
              <select
                name="goal"
                value={formData.goal}
                onChange={handleInputChange}
                className="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-white"
              >
                <option value="fat_loss">Pérdida de Grasa Preservando Músculo</option>
                <option value="muscle">Hipertrofia / Ganancia Muscular</option>
                <option value="recomp">Recomposición Corporal</option>
                <option value="health">Salud General y Acondicionamiento</option>
              </select>
            </div>

            <div className="pt-2 border-t border-slate-700/60">
              <h3 className="text-xs font-semibold uppercase tracking-wider text-amber-400 mb-3">
                Molestias o Condición Médica
              </h3>
              <div className="space-y-2">
                {[
                  { id: 'lumbar', label: 'Zona Lumbar / Hernia' },
                  { id: 'shoulder', label: 'Hombro / Manguito Rotador' },
                  { id: 'knee', label: 'Rodillas (Condromalacia)' },
                  { id: 'hypertension', label: 'Hipertensión Arterial' }
                ].map(item => (
                  <label key={item.id} className="flex items-center space-x-3 text-sm text-slate-300 cursor-pointer">
                    <input
                      type="checkbox"
                      checked={formData.conditions[item.id]}
                      onChange={() => handleCheckboxChange(item.id)}
                      className="rounded bg-slate-900 border-slate-700 text-emerald-500 h-4 w-4"
                    />
                    <span>{item.label}</span>
                  </label>
                ))}
              </div>
            </div>
          </div>

          {/* SEGUIMIENTO DE ENTRENAMIENTOS */}
          <div className={`${theme.card} rounded-2xl p-6 print-card`}>
            <div className="flex justify-between items-center mb-4">
              <h3 className={`text-sm font-semibold ${theme.accentText} print:text-black`}>
                Registro Semanal de Sesiones
              </h3>
              <button onClick={resetProgress} className="text-[11px] text-slate-400 hover:text-white underline no-print">
                Reiniciar
              </button>
            </div>

            <div className="flex justify-between gap-2">
              {Array.from({ length: parseInt(formData.daysPerWeek) }).map((_, idx) => {
                const dayNum = idx + 1;
                const isDone = completedSessions.includes(dayNum);
                return (
                  <button
                    key={dayNum}
                    onClick={() => toggleSessionCompleted(dayNum)}
                    className={`flex-1 py-3 rounded-xl font-bold text-xs border transition-all cursor-pointer ${
                      isDone
                        ? `${theme.accentBg} text-slate-950 font-bold border-transparent print:bg-gray-300 print:text-black`
                        : 'bg-slate-900/80 text-slate-400 border-slate-700 hover:border-slate-500 print:bg-white print:border-gray-300'
                    }`}
                  >
                    Día {dayNum} {isDone ? '✓' : ''}
                  </button>
                );
              })}
            </div>
          </div>
        </section>

        {/* COLUMNA DERECHA: COMPOSICIÓN & RUTINA CON MODAL DE ANIMACIÓN */}
        <section className="lg:col-span-8 space-y-6">
          {/* DIAGNÓSTICO CORPORAL */}
          <div className={`${theme.card} rounded-2xl p-6 print-card`}>
            <h2 className={`text-lg font-semibold ${theme.accentText} mb-4 print:text-black`}>
              2. Diagnóstico de Composición Corporal
            </h2>

            <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
              <div className={`${theme.innerCard} p-4 rounded-xl text-center print:bg-white print:border-gray-300`}>
                <span className="text-xs text-slate-400 print:text-gray-600">Masa Magra</span>
                <p className={`text-2xl font-bold ${theme.accentText} print:text-black`}>{metrics.leanMassKg} <span className="text-xs">kg</span></p>
              </div>
              <div className={`${theme.innerCard} p-4 rounded-xl text-center print:bg-white print:border-gray-300`}>
                <span className="text-xs text-slate-400 print:text-gray-600">Masa Grasa</span>
                <p className="text-2xl font-bold text-amber-400 print:text-black">{metrics.fatMassKg} <span className="text-xs">kg</span></p>
              </div>
              <div className={`${theme.innerCard} p-4 rounded-xl text-center print:bg-white print:border-gray-300`}>
                <span className="text-xs text-slate-400 print:text-gray-600">IMC</span>
                <p className="text-2xl font-bold text-white print:text-black">{metrics.bmi}</p>
              </div>
              <div className={`${theme.innerCard} p-4 rounded-xl text-center print:bg-white print:border-gray-300`}>
                <span className="text-xs text-slate-400 print:text-gray-600">FFMI</span>
                <p className="text-2xl font-bold text-teal-300 print:text-black">{metrics.ffmi}</p>
              </div>
            </div>
          </div>

          {/* EJERCICIOS Y ANIMACIONES */}
          <div className={`${theme.card} rounded-2xl p-6 print-card`}>
            <div className="flex justify-between items-center mb-4">
              <h2 className={`text-lg font-semibold ${theme.accentText} print:text-black`}>
                3. Ejercicios Adaptados
              </h2>
              {activeConditionsKeys.length > 0 && (
                <span className="bg-amber-500/10 text-amber-400 border border-amber-500/30 text-xs px-2.5 py-1 rounded-md print:bg-gray-200 print:text-black">
                  {activeConditionsKeys.length} adaptación(es) activa(s)
                </span>
              )}
            </div>

            <div className="space-y-6">
              {Object.entries(groupedExercises).map(([category, items]) => (
                <div key={category} className="space-y-3">
                  <h3 className="text-sm font-bold text-slate-300 uppercase tracking-wider border-b border-slate-700/60 pb-1 print:text-black print:border-gray-300">
                    Patrón: {category} ({items.length})
                  </h3>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
                    {items.map((ex) => (
                      <div
                        key={ex.id}
                        onClick={() => setSelectedExerciseModal(ex)}
                        className={`p-3 rounded-xl border text-sm cursor-pointer transition-all ${
                          ex.isAdapted
                            ? 'bg-amber-950/20 border-amber-500/40 print:bg-gray-100'
                            : `${theme.innerCard} hover:border-emerald-500/50 print:bg-white print:border-gray-300`
                        }`}
                      >
                        <div className="flex justify-between items-start mb-1">
                          <span className="font-semibold text-white print:text-black">{ex.name}</span>
                          <span className={`text-xs font-mono ${theme.accentText} bg-slate-950 px-2 py-0.5 rounded border border-slate-800 print:bg-gray-200 print:text-black`}>
                            {ex.setsReps}
                          </span>
                        </div>

                        {ex.isAdapted ? (
                          <div className="mt-2 text-xs text-amber-300/90 bg-amber-900/20 p-2 rounded border border-amber-800/30 print:bg-gray-200 print:text-black">
                            <span className="font-bold">✓ Adaptado:</span> Reemplaza a <em>"{ex.originalName}"</em>.
                          </div>
                        ) : (
                          <div className="flex justify-between items-center mt-2 no-print">
                            <span className="text-[11px] text-slate-400">Tocar para técnica y animación</span>
                            <span className={`text-xs ${theme.accentText} font-bold`}>▶ Ver</span>
                          </div>
                        )}
                      </div>
                    ))}
                  </div>
                </div>
              ))}
            </div>
          </div>
        </section>
      </main>

      {/* MODAL DE ANIMACIÓN E INSTRUCCIONES */}
      {selectedExerciseModal && (
        <div className="fixed inset-0 bg-slate-950/80 backdrop-blur-sm flex items-center justify-center p-4 z-50 no-print">
          <div className={`${theme.card} max-w-lg w-full rounded-2xl p-6 relative border shadow-2xl`}>
            
            <button
              onClick={() => setSelectedExerciseModal(null)}
              className="absolute top-4 right-4 text-slate-400 hover:text-white text-lg font-bold cursor-pointer"
            >
              ✕
            </button>

            <h3 className={`text-xl font-bold ${theme.accentText} mb-1`}>
              {selectedExerciseModal.name}
            </h3>
            <p className="text-xs text-slate-400 mb-4">Guía biomecánica de ejecución en Fitvic</p>

            {/* VISOR VECTORIAL DE ANIMACIÓN */}
            <div className="w-full h-44 bg-slate-950 rounded-xl border border-slate-800 flex flex-col items-center justify-center relative overflow-hidden mb-4">
              <div className="w-10 h-10 rounded-full border-4 border-emerald-400 border-t-transparent animate-spin mb-2"></div>
              <span className="text-xs text-emerald-400 font-mono animate-pulse">
                [ Demostración 2D: {selectedExerciseModal.animationType?.toUpperCase()} ]
              </span>
            </div>

            {/* INSTRUCCIONES */}
            <div className="space-y-3 mb-4">
              <h4 className="text-xs font-bold uppercase text-slate-300 tracking-wider">Pasos técnicos:</h4>
              <ul className="space-y-1.5 text-xs text-slate-300">
                {selectedExerciseModal.steps?.map((step, idx) => (
                  <li key={idx} className="flex items-start space-x-2">
                    <span className={`${theme.accentText} font-bold`}>{idx + 1}.</span>
                    <span>{step}</span>
                  </li>
                ))}
              </ul>
            </div>

            {/* TIP DE SEGURIDAD */}
            {selectedExerciseModal.tip && (
              <div className="bg-emerald-950/30 border border-emerald-500/30 p-3 rounded-xl text-xs text-emerald-300">
                <strong>💡 Clave de seguridad:</strong> {selectedExerciseModal.tip}
              </div>
            )}
          </div>
        </div>
      )}
    </div>
  );
}
