<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Great Salt Lake Sentinel Monitoring & Alert Layer</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- React & ReactDOM UMD -->
  <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
  <!-- Babel Standalone -->
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body class="bg-slate-950 text-slate-100 font-sans antialiased selection:bg-cyan-500 selection:text-slate-950 min-h-screen">
  <div id="root"></div>

  <script type="text/babel">
    const { useState, useEffect, useMemo } = React;

    // --- INLINE SVG ICONS (Zero external package dependencies) ---
    const ActivityIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
      </svg>
    );

    const AlertTriangleIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
      </svg>
    );

    const WindIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M9.59 4.59A2 2 0 1111 8H2m10.59 11.41A2 2 0 1014 16H2m15.73-8.27A2.5 2.5 0 1119.5 12H2" />
      </svg>
    );

    const DropletsIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M12 2.69l5.66 5.66a8 8 0 11-11.31 0z" />
      </svg>
    );

    const CompassIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M12 20.25c4.556 0 8.25-3.694 8.25-8.25S16.556 3.75 12 3.75 3.75 7.444 3.75 12s3.694 8.25 8.25 8.25z" />
        <path strokeLinecap="round" strokeLinejoin="round" d="M16.243 7.757l-2.121 6.364-6.364 2.121 2.121-6.364 6.364-2.121z" />
      </svg>
    );

    const ShieldAlertIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
      </svg>
    );

    const SlidersIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M10.5 6a7.5 7.5 0 107.5 7.5h-7.5V6z" />
        <path strokeLinecap="round" strokeLinejoin="round" d="M13.5 10.5H21A7.5 7.5 0 0013.5 3v7.5z" />
      </svg>
    );

    const TerminalIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M8 9l3 3-3 3m5 0h3M5 20h14a2 2 0 002-2V6a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
      </svg>
    );

    const RadioIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M5.636 18.364a9 9 0 010-12.728m12.728 0a9 9 0 010 12.728m-9.9-2.829a5 5 0 010-7.07m7.072 0a5 5 0 010 7.07M13 12a1 1 0 11-2 0 1 1 0 012 0z" />
      </svg>
    );

    const InfoIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
    );

    const CheckCircleIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
    );

    const DownloadIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
      </svg>
    );

    const SparklesIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-16l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z" />
      </svg>
    );

    const VolumeIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z" />
      </svg>
    );

    const LayersIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" />
      </svg>
    );

    const TrendingUpIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6" />
      </svg>
    );

    const ZapIcon = ({ className = "w-4 h-4" }) => (
      <svg className={className} fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
        <path strokeLinecap="round" strokeLinejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
      </svg>
    );

    // --- STATION DATA DEFINITIONS ---
    const USGS_STATIONS = [
      { id: '10010000', name: 'Saltair South Arm (10010000)', lat: 40.758, lng: -112.202, arm: 'South', baseElev: 4190.43 },
      { id: '10010100', name: 'Saline North Arm (10010100)', lat: 41.231, lng: -112.712, arm: 'North', baseElev: 4189.82 },
      { id: '10141000', name: 'Weber River near Plain City', lat: 41.272, lng: -112.098, arm: 'Inflow', flow: 420 },
      { id: '10171000', name: 'Jordan River at Salt Lake City', lat: 40.738, lng: -111.921, arm: 'Inflow', flow: 215 },
      { id: '10131000', name: 'Bear River near Corinne', lat: 41.549, lng: -112.181, arm: 'Inflow', flow: 1180 }
    ];

    const UDORN_STATIONS = [
      { id: 'UDORN-01', name: 'Antelope Island Ridge', PM10: 42, arsenicIndex: 0.78, status: 'Active' },
      { id: 'UDORN-02', name: 'Syracuse Playa West', PM10: 118, arsenicIndex: 1.45, status: 'Warning' },
      { id: 'UDORN-03', name: 'Promontory Point South', PM10: 89, arsenicIndex: 1.12, status: 'Active' },
      { id: 'UDORN-04', name: 'Farmington Bay Mudflats', PM10: 165, arsenicIndex: 2.10, status: 'Critical' },
      { id: 'UDORN-05', name: 'Tooele Valley North', PM10: 55, arsenicIndex: 0.65, status: 'Active' }
    ];

    // --- MAIN REACT APPLICATION COMPONENT ---
    function App() {
      // Sliders & Telemetry State
      const [waterElevation, setWaterElevation] = useState(4190.43);
      const [windSpeed, setWindSpeed] = useState(24.5);
      const [windDirection, setWindDirection] = useState('WSW');
      const [crustErosionIndex, setCrustErosionIndex] = useState(0.82);

      // Simulation System State
      const [autoPulse, setAutoPulse] = useState(true);
      const [lastSync, setLastSync] = useState(new Date().toISOString());
      const [activeTab, setActiveTab] = useState('overview');
      const [copylog, setCopylog] = useState(false);

      // Gemini AI Integration State
      const [aiAnalysis, setAiAnalysis] = useState("");
      const [isGeneratingAi, setIsGeneratingAi] = useState(false);
      const [aiAudioPlaying, setAiAudioPlaying] = useState(false);

      // Automated Pulse Simulator
      useEffect(() => {
        if (!autoPulse) return;
        const interval = setInterval(() => {
          setWindSpeed(prev => Math.max(5, Math.min(65, +(prev + (Math.random() - 0.48) * 1.5).toFixed(1))));
          setLastSync(new Date().toISOString());
        }, 4000);
        return () => clearInterval(interval);
      }, [autoPulse]);

      // Exponential & Logarithmic Risk Calculations
      const calculations = useMemo(() => {
        const elevDelta = 4200 - waterElevation;
        const maxPlayaSqMiles = 1250;
        const exposedPlayaSqMiles = Math.min(
          maxPlayaSqMiles, 
          Math.max(0, +(280 + elevDelta * 115.5 + Math.pow(elevDelta, 1.4) * 18).toFixed(1))
        );
        const exposedPlayaPct = +((exposedPlayaSqMiles / 2100) * 100).toFixed(1);
        const volumePct = Math.max(5, Math.min(100, +(100 - elevDelta * 8.5 - Math.pow(elevDelta, 1.2) * 1.1).toFixed(1)));

        const vRatio = Math.max(1.05, 100 / volumePct);
        const southSalinityPct = +(12.5 + 3.8 * Math.log(vRatio)).toFixed(1);
        const northSalinityPct = +(28.0 + 5.2 * Math.log(vRatio)).toFixed(1);
        const brineShrimpHealth = southSalinityPct > 17.5 ? 'CRITICAL_COLLAPSE' : southSalinityPct > 15.0 ? 'SEVERE_STRESS' : 'OPTIMAL';

        const alpha = 0.045;
        const beta = 0.38;
        const thresholdWindSpeed = 14.0;
        const excessWind = Math.max(0, windSpeed - thresholdWindSpeed);
        const hydroDelta = Math.max(0, 4198.0 - waterElevation);
        const dustFluxVal = alpha * Math.pow(excessWind, 3) * Math.exp(beta * hydroDelta) * crustErosionIndex;
        const normalizedDustRisk = Math.min(100, +(dustFluxVal / 3.5).toFixed(1));

        const estimatedPM10 = +(22 + normalizedDustRisk * 4.8).toFixed(0);
        const arsenicToxFactor = +(1.0 + (hydroDelta * 0.4) + (crustErosionIndex * 0.5)).toFixed(2);

        let riskLevel = 'NOMINAL';
        let alertCode = 'STRIKE_TEAM_STABLE';

        if (waterElevation < 4189.5 || normalizedDustRisk > 75 || southSalinityPct > 17.0) {
          riskLevel = 'CRITICAL';
          alertCode = 'STRIKE_TEAM_CRITICAL';
        } else if (waterElevation < 4192.0 || normalizedDustRisk > 45 || southSalinityPct > 14.5) {
          riskLevel = 'WARNING';
          alertCode = 'STRIKE_TEAM_ELEVATED';
        } else if (waterElevation < 4195.0 || normalizedDustRisk > 25) {
          riskLevel = 'WATCH';
          alertCode = 'STRIKE_TEAM_WATCH';
        }

        return {
          exposedPlayaSqMiles,
          exposedPlayaPct,
          volumePct,
          southSalinityPct,
          northSalinityPct,
          brineShrimpHealth,
          dustFluxVal: +dustFluxVal.toFixed(2),
          normalizedDustRisk,
          estimatedPM10,
          arsenicToxFactor,
          riskLevel,
          alertCode
        };
      }, [waterElevation, windSpeed, crustErosionIndex]);

      // x402 Header and JSON schema
      const x402AlertHeader = useMemo(() => {
        return {
          "gsl_sentinel_alert": {
            "timestamp_utc": lastSync,
            "x402_header": `X-GSL-ALERT: status=${calculations.alertCode}; elev=${waterElevation}ft; pm10=${calculations.estimatedPM10}ugm3; signature=sha256_0x9f41b`,
            "alert_level": calculations.alertCode,
            "telemetry": {
              "south_arm_elevation_ft": waterElevation,
              "exposed_playa_percentage": calculations.exposedPlayaPct,
              "exposed_playa_sq_miles": calculations.exposedPlayaSqMiles,
              "lake_volume_status_pct": calculations.volumePct
            },
            "modeled_forecast": {
              "south_arm_salinity_pct": calculations.southSalinityPct,
              "north_arm_salinity_pct": calculations.northSalinityPct,
              "wasatch_front_pm10_index": calculations.estimatedPM10,
              "arsenic_bioavailability_multiplier": calculations.arsenicToxFactor,
              "calculated_dust_flux": calculations.dustFluxVal
            },
            "action_protocol": calculations.riskLevel === 'CRITICAL' 
              ? "TRIGGER_UDORN_WASATCH_FRONT_EMERGENCY_BROADCAST" 
              : calculations.riskLevel === 'WARNING' 
                ? "ACTIVATE_AIR_QUALITY_ADVISORY_TIER_2" 
                : "ROUTINE_MONITORING_SWEEP"
          }
        };
      }, [waterElevation, lastSync, calculations]);

      // Exponential Retry Helper for Gemini API
      const fetchWithBackoff = async (url, options, retries = 5, delay = 1000) => {
        try {
          const res = await fetch(url, options);
          if (!res.ok) throw new Error(`HTTP ${res.status}`);
          return await res.json();
        } catch (err) {
          if (retries > 0) {
            await new Promise(r => setTimeout(r, delay));
            return fetchWithBackoff(url, options, retries - 1, delay * 2);
          }
          throw err;
        }
      };

      // Gemini AI Text Generation
      const fetchGeminiSynthesis = async () => {
        setIsGeneratingAi(true);
        setAiAnalysis("");
        const apiKey = ""; // Provided at runtime

        const prompt = `You are Chief Hydro-geochemist for Great Salt Lake Sentinel Layer (GSL-SAL).
Current metrics:
- Elevation: ${waterElevation} ft MSL
- Exposed Lakebed: ${calculations.exposedPlayaPct}% (${calculations.exposedPlayaSqMiles} sq miles)
- South Arm Salinity: ${calculations.southSalinityPct}%
- Dust Flux Risk: ${calculations.normalizedDustRisk}/100 (PM10: ${calculations.estimatedPM10} µg/m³)
- Arsenic Multiplier: ${calculations.arsenicToxFactor}x
- Wind: ${windSpeed} mph ${windDirection}
- Alert Status: ${calculations.alertCode}

Provide 3 concise executive bullet points on immediate risks, toxic dust threats to Wasatch Front, and emergency actions.`;

        try {
          const data = await fetchWithBackoff(
            `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`,
            {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify({
                contents: [{ parts: [{ text: prompt }] }],
                systemInstruction: { parts: [{ text: "You are an environmental risk analysis engine." }] }
              })
            }
          );
          const text = data.candidates?.[0]?.content?.parts?.[0]?.text;
          setAiAnalysis(text || "Analysis completed.");
        } catch (e) {
          setAiAnalysis("Error communicating with intelligence engine.");
        } finally {
          setIsGeneratingAi(false);
        }
      };

      // Gemini Text-To-Speech Playback
      const speakSynthesis = async () => {
        if (!aiAnalysis) return;
        setAiAudioPlaying(true);
        const apiKey = "";

        const cleanText = `Sentinel Alert ${calculations.riskLevel}. ${aiAnalysis.replace(/[*#]/g, '')}`;

        try {
          const data = await fetchWithBackoff(
            `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-tts:generateContent?key=${apiKey}`,
            {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify({
                contents: [{ parts: [{ text: `Say with urgent authority: ${cleanText.slice(0, 280)}` }] }],
                generationConfig: { 
                  responseModalities: ["AUDIO"], 
                  speechConfig: { voiceConfig: { prebuiltVoiceConfig: { voiceName: "Kore" } } } 
                },
                model: "gemini-2.5-flash-preview-tts"
              })
            }
          );

          const audioPart = data.candidates?.[0]?.content?.parts?.find(p => p.inlineData);
          if (audioPart && audioPart.inlineData) {
            const base64Pcm = audioPart.inlineData.data;
            const mimeType = audioPart.inlineData.mimeType || "audio/L16;rate=24000";
            const sampleRate = parseInt(mimeType.match(/rate=(\d+)/)?.[1] || "24000", 10);
            
            // Convert PCM16 Base64 to Web Audio Buffer
            const binaryStr = atob(base64Pcm);
            const bytes = new Uint8Array(binaryStr.length);
            for (let i = 0; i < binaryStr.length; i++) bytes[i] = binaryStr.charCodeAt(i);
            
            const audioCtx = new (window.AudioContext || window.webkitAudioContext)({ sampleRate });
            const pcm16 = new Int16Array(bytes.buffer);
            const buffer = audioCtx.createBuffer(1, pcm16.length, sampleRate);
            const channelData = buffer.getChannelData(0);
            for (let i = 0; i < pcm16.length; i++) channelData[i] = pcm16[i] / 32768.0;

            const source = audioCtx.createBufferSource();
            source.buffer = buffer;
            source.connect(audioCtx.destination);
            source.onended = () => setAiAudioPlaying(false);
            source.start();
          } else {
            setAiAudioPlaying(false);
          }
        } catch (e) {
          setAiAudioPlaying(false);
        }
      };

      // Fallback copy to clipboard using execCommand
      const copyPayloadToClipboard = () => {
        const text = JSON.stringify(x402AlertHeader, null, 2);
        const textArea = document.createElement("textarea");
        textArea.value = text;
        document.body.appendChild(textArea);
        textArea.select();
        try {
          document.execCommand('copy');
          setCopylog(true);
          setTimeout(() => setCopylog(false), 2000);
        } catch (err) {
          console.error(err);
        }
        document.body.removeChild(textArea);
      };

      return (
        <div className="flex flex-col min-h-screen">
          
          {/* TOP MASTER TELEMETRY HEADER */}
          <header className="bg-slate-900 border-b border-slate-800 px-4 py-3 sticky top-0 z-50 shadow-2xl backdrop-blur bg-opacity-95">
            <div className="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-4">
              
              <div className="flex items-center gap-3">
                <div className="relative">
                  <div className="w-10 h-10 rounded-lg bg-gradient-to-br from-cyan-500 to-blue-700 flex items-center justify-center text-slate-950 font-black shadow-lg shadow-cyan-500/20">
                    <ActivityIcon className="w-6 h-6 text-slate-950 animate-pulse" />
                  </div>
                  <span className="absolute -bottom-1 -right-1 flex h-3 w-3">
                    <span className="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
                    <span className="relative inline-flex rounded-full h-3 w-3 bg-emerald-500"></span>
                  </span>
                </div>
                <div>
                  <h1 className="text-lg font-bold tracking-tight text-white flex items-center gap-2">
                    GSL SENTINEL MONITORING & ALERT LAYER
                    <span className="text-xs px-2 py-0.5 rounded bg-cyan-950 text-cyan-400 border border-cyan-800 font-mono">v3.4-PROD</span>
                  </h1>
                  <p className="text-xs text-slate-400 font-mono">ESTUARY EXECUTION ENGINE • USGS / UDORN NODE SYNC</p>
                </div>
              </div>

              {/* Master System Telemetry Badges */}
              <div className="grid grid-cols-2 sm:grid-cols-4 gap-3 w-full md:w-auto font-mono text-xs">
                <div className="bg-slate-950 border border-slate-800 p-2 rounded-lg">
                  <span className="text-slate-500 block text-[10px] uppercase">South Arm Pool</span>
                  <span className={`font-bold text-sm ${waterElevation < 4192.0 ? 'text-amber-400' : 'text-cyan-400'}`}>
                    {waterElevation.toFixed(2)} ft MSL
                  </span>
                </div>

                <div className="bg-slate-950 border border-slate-800 p-2 rounded-lg">
                  <span className="text-slate-500 block text-[10px] uppercase">Lake Volume Status</span>
                  <span className="font-bold text-sm text-slate-200">
                    {calculations.volumePct}% <span className="text-slate-500 text-[10px]">Full</span>
                  </span>
                </div>

                <div className="bg-slate-950 border border-slate-800 p-2 rounded-lg">
                  <span className="text-slate-500 block text-[10px] uppercase">Exposed Lakebed</span>
                  <span className="font-bold text-sm text-amber-400">
                    {calculations.exposedPlayaPct}% <span className="text-slate-400 text-[10px]">({calculations.exposedPlayaSqMiles} sq mi)</span>
                  </span>
                </div>

                <div className={`bg-slate-950 border p-2 rounded-lg flex items-center justify-between ${
                  calculations.riskLevel === 'CRITICAL' 
                    ? 'border-red-500/50 text-red-400 bg-red-950/20' 
                    : calculations.riskLevel === 'WARNING' 
                      ? 'border-amber-500/50 text-amber-400 bg-amber-950/20' 
                      : 'border-emerald-500/50 text-emerald-400 bg-emerald-950/20'
                }`}>
                  <div>
                    <span className="text-slate-500 block text-[10px] uppercase">Status Code</span>
                    <span className="font-bold text-xs font-mono">{calculations.alertCode}</span>
                  </div>
                  <ShieldAlertIcon className="w-4 h-4 ml-1" />
                </div>
              </div>

              {/* Actions & Stream Simulator Buttons */}
              <div className="flex items-center gap-2">
                <button 
                  onClick={() => setAutoPulse(!autoPulse)}
                  className={`p-2 rounded-lg text-xs font-medium border flex items-center gap-1.5 transition ${
                    autoPulse 
                      ? 'bg-emerald-950/50 border-emerald-700/50 text-emerald-300' 
                      : 'bg-slate-800 border-slate-700 text-slate-400'
                  }`}
                >
                  <RadioIcon className={`w-3.5 h-3.5 ${autoPulse ? 'animate-pulse text-emerald-400' : ''}`} />
                  <span className="hidden sm:inline">{autoPulse ? 'Live Feed' : 'Paused'}</span>
                </button>

                <button 
                  onClick={fetchGeminiSynthesis}
                  disabled={isGeneratingAi}
                  className="bg-gradient-to-r from-cyan-600 to-blue-600 hover:from-cyan-500 hover:to-blue-500 text-white font-medium text-xs px-3 py-2 rounded-lg flex items-center gap-1.5 shadow-md shadow-cyan-900/30 transition disabled:opacity-50"
                >
                  <SparklesIcon className="w-3.5 h-3.5 text-cyan-200" />
                  <span>{isGeneratingAi ? 'Analyzing...' : 'AI Brief'}</span>
                </button>
              </div>

            </div>
          </header>

          {/* NAVIGATION TABS */}
          <div className="bg-slate-900/60 border-b border-slate-800 px-4">
            <div className="max-w-7xl mx-auto flex items-center gap-2 overflow-x-auto py-2 font-mono text-xs">
              {[
                { id: 'overview', name: 'Integrated Dashboard', icon: LayersIcon },
                { id: 'hydrology', name: 'Hydrological Balancing', icon: DropletsIcon },
                { id: 'dust', name: 'Toxic Dust Viewport', icon: WindIcon },
                { id: 'api', name: 'x402 Emergency Payload', icon: TerminalIcon }
              ].map(tab => {
                const Icon = tab.icon;
                const isActive = activeTab === tab.id;
                return (
                  <button
                    key={tab.id}
                    onClick={() => setActiveTab(tab.id)}
                    className={`flex items-center gap-2 px-3 py-1.5 rounded-md transition whitespace-nowrap ${
                      isActive 
                        ? 'bg-cyan-500/10 text-cyan-400 border border-cyan-500/30 font-semibold' 
                        : 'text-slate-400 hover:text-slate-200 hover:bg-slate-800/50'
                    }`}
                  >
                    <Icon className="w-3.5 h-3.5" />
                    <span>{tab.name}</span>
                  </button>
                );
              })}
            </div>
          </div>

          {/* GEMINI AI SYNTHESIS BANNER */}
          {(aiAnalysis || isGeneratingAi) && (
            <div className="bg-gradient-to-r from-slate-900 via-cyan-950/40 to-slate-900 border-b border-cyan-500/30 px-4 py-3">
              <div className="max-w-7xl mx-auto flex flex-col md:flex-row items-start md:items-center justify-between gap-3 text-xs">
                <div className="flex items-start gap-2.5">
                  <SparklesIcon className="w-4 h-4 text-cyan-400 mt-0.5 shrink-0" />
                  <div>
                    <span className="font-bold text-cyan-300 font-mono block mb-0.5">GEMINI SENTINEL INTELLIGENCE BRIEF</span>
                    {isGeneratingAi ? (
                      <p className="text-slate-400 animate-pulse">Running hydro-chemical stack simulation and particulate risk assessment...</p>
                    ) : (
                      <div className="text-slate-200 font-mono whitespace-pre-line leading-relaxed">{aiAnalysis}</div>
                    )}
                  </div>
                </div>

                {aiAnalysis && !isGeneratingAi && (
                  <button
                    onClick={speakSynthesis}
                    disabled={aiAudioPlaying}
                    className="bg-cyan-500/20 hover:bg-cyan-500/30 text-cyan-300 border border-cyan-500/40 px-3 py-1.5 rounded flex items-center gap-2 shrink-0 transition"
                  >
                    <VolumeIcon className={`w-3.5 h-3.5 ${aiAudioPlaying ? 'animate-bounce text-cyan-400' : ''}`} />
                    <span>{aiAudioPlaying ? 'Broadcasting...' : 'Audio Dispatch'}</span>
                  </button>
                )}
              </div>
            </div>
          )}

          {/* MAIN WORKSPACE CONTENT */}
          <main className="flex-1 max-w-7xl w-full mx-auto p-4 space-y-6">

            {/* INTERACTIVE ENVIRONMENTAL PARAMETER SLIDERS */}
            <section className="bg-slate-900 border border-slate-800 rounded-xl p-4 shadow-xl">
              <div className="flex items-center justify-between border-b border-slate-800 pb-2 mb-4">
                <div className="flex items-center gap-2">
                  <SlidersIcon className="w-4 h-4 text-cyan-400" />
                  <h2 className="text-xs font-bold uppercase tracking-wider font-mono text-slate-300">
                    Interactive Environmental Stress Simulator
                  </h2>
                </div>
                <span className="text-[11px] text-slate-500 font-mono">Real-time dynamic model feedback</span>
              </div>

              <div className="grid grid-cols-1 md:grid-cols-4 gap-6 text-xs">
                
                {/* Elevation Slider */}
                <div className="space-y-2">
                  <div className="flex justify-between font-mono">
                    <label className="text-slate-400">South Arm Level (ft MSL)</label>
                    <span className="font-bold text-cyan-400">{waterElevation.toFixed(2)} ft</span>
                  </div>
                  <input 
                    type="range" 
                    min="4185.0" 
                    max="4202.0" 
                    step="0.1" 
                    value={waterElevation}
                    onChange={(e) => setWaterElevation(parseFloat(e.target.value))}
                    className="w-full h-2 bg-slate-800 rounded-lg appearance-none cursor-pointer accent-cyan-500"
                  />
                  <div className="flex justify-between text-[10px] text-slate-500 font-mono">
                    <span>4185.0 (Low)</span>
                    <span>4198.0 (Target)</span>
                    <span>4202.0 (High)</span>
                  </div>
                </div>

                {/* Wind Speed Slider */}
                <div className="space-y-2">
                  <div className="flex justify-between font-mono">
                    <label className="text-slate-400">Wasatch Wind Velocity</label>
                    <span className="font-bold text-amber-400">{windSpeed} mph</span>
                  </div>
                  <input 
                    type="range" 
                    min="5" 
                    max="65" 
                    step="1" 
                    value={windSpeed}
                    onChange={(e) => setWindSpeed(parseFloat(e.target.value))}
                    className="w-full h-2 bg-slate-800 rounded-lg appearance-none cursor-pointer accent-amber-500"
                  />
                  <div className="flex justify-between text-[10px] text-slate-500 font-mono">
                    <span>5 mph</span>
                    <span>14 mph (Threshold)</span>
                    <span>65 mph</span>
                  </div>
                </div>

                {/* Crust Erosion Multiplier Slider */}
                <div className="space-y-2">
                  <div className="flex justify-between font-mono">
                    <label className="text-slate-400">Playa Crust Erosion Multiplier</label>
                    <span className="font-bold text-red-400">{crustErosionIndex}x</span>
                  </div>
                  <input 
                    type="range" 
                    min="0.2" 
                    max="2.0" 
                    step="0.05" 
                    value={crustErosionIndex}
                    onChange={(e) => setCrustErosionIndex(parseFloat(e.target.value))}
                    className="w-full h-2 bg-slate-800 rounded-lg appearance-none cursor-pointer accent-red-500"
                  />
                  <div className="flex justify-between text-[10px] text-slate-500 font-mono">
                    <span>0.2x (Crusted)</span>
                    <span>1.0x (Standard)</span>
                    <span>2.0x (Shattered)</span>
                  </div>
                </div>

                {/* Wind Direction Selector */}
                <div className="space-y-2">
                  <label className="text-slate-400 block font-mono">Dominant Shear Direction</label>
                  <div className="grid grid-cols-4 gap-1.5 font-mono text-[11px]">
                    {['WSW', 'NW', 'SSW', 'E'].map(dir => (
                      <button
                        key={dir}
                        onClick={() => setWindDirection(dir)}
                        className={`py-1.5 rounded border text-center transition ${
                          windDirection === dir 
                            ? 'bg-cyan-500/20 border-cyan-500 text-cyan-300 font-bold' 
                            : 'bg-slate-950 border-slate-800 text-slate-400 hover:bg-slate-800'
                        }`}
                      >
                        {dir}
                      </button>
                    ))}
                  </div>
                </div>

              </div>
            </section>

            {/* DASHBOARD MAIN PANELS */}
            {(activeTab === 'overview' || activeTab === 'hydrology') && (
              <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
                
                {/* HYDROLOGICAL BALANCING ENGINE */}
                <div className="bg-slate-900 border border-slate-800 rounded-xl overflow-hidden shadow-xl flex flex-col">
                  
                  <div className="p-4 bg-slate-900 border-b border-slate-800 flex items-center justify-between">
                    <div className="flex items-center gap-2">
                      <div className="p-1.5 rounded bg-blue-500/10 text-blue-400 border border-blue-500/20">
                        <DropletsIcon className="w-4 h-4" />
                      </div>
                      <div>
                        <h3 className="font-bold text-sm text-slate-100 font-mono">HYDROLOGICAL BALANCING ENGINE</h3>
                        <p className="text-[11px] text-slate-400 font-mono">Gilbert vs. Gunnison Bays Dynamics</p>
                      </div>
                    </div>
                    <span className="text-[10px] font-mono px-2 py-0.5 rounded bg-slate-800 text-slate-300">USGS LIVE</span>
                  </div>

                  <div className="p-5 space-y-6 flex-1">

                    {/* Salinity Formula */}
                    <div className="bg-slate-950 border border-slate-800 rounded-lg p-4 font-mono text-xs space-y-3">
                      <div className="flex items-center justify-between text-slate-400 border-b border-slate-800 pb-2">
                        <span className="flex items-center gap-1 text-cyan-400 font-semibold">
                          <InfoIcon className="w-3.5 h-3.5" /> Salinity Concentration Formula
                        </span>
                        <span className="text-[10px]">Logarithmic Model</span>
                      </div>

                      <div className="bg-slate-900 p-2.5 rounded text-center text-cyan-300 text-xs tracking-wider border border-slate-800">
                        ΔC_salt = k · ln ( V_historic / V_current )
                      </div>

                      <div className="grid grid-cols-2 gap-3 pt-1">
                        <div className="p-2.5 bg-slate-900/50 rounded border border-slate-800">
                          <span className="text-slate-500 block text-[10px]">South Arm (Gilbert Bay)</span>
                          <span className={`text-base font-bold ${calculations.southSalinityPct > 15.0 ? 'text-amber-400' : 'text-cyan-400'}`}>
                            {calculations.southSalinityPct}% <span className="text-slate-400 text-[10px]">salinity</span>
                          </span>
                        </div>

                        <div className="p-2.5 bg-slate-900/50 rounded border border-slate-800">
                          <span className="text-slate-500 block text-[10px]">North Arm (Gunnison Bay)</span>
                          <span className="text-base font-bold text-purple-400">
                            {calculations.northSalinityPct}% <span className="text-slate-400 text-[10px]">hyper-saline</span>
                          </span>
                        </div>
                      </div>
                    </div>

                    {/* Brine Shrimp Ecosystem Tipping Point Warning */}
                    <div className={`p-3.5 rounded-lg border flex items-start gap-3 font-mono text-xs ${
                      calculations.brineShrimpHealth === 'CRITICAL_COLLAPSE'
                        ? 'bg-red-950/30 border-red-500/40 text-red-300'
                        : calculations.brineShrimpHealth === 'SEVERE_STRESS'
                          ? 'bg-amber-950/30 border-amber-500/40 text-amber-300'
                          : 'bg-emerald-950/20 border-emerald-500/30 text-emerald-300'
                    }`}>
                      <AlertTriangleIcon className="w-4 h-4 shrink-0 mt-0.5" />
                      <div>
                        <span className="font-bold block">
                          Ecological Viability: {calculations.brineShrimpHealth.replace('_', ' ')}
                        </span>
                        <p className="text-[11px] text-slate-400 mt-0.5">
                          {calculations.southSalinityPct > 16.0 
                            ? 'Brine shrimp (Artemia) cyst production halted. Avian foraging chain threatened.'
                            : 'Salinity within tolerable limits for brine shrimp and shorebird populations.'}
                        </p>
                      </div>
                    </div>

                    {/* USGS Inflow Telemetry Table */}
                    <div>
                      <h4 className="text-xs font-bold font-mono text-slate-300 uppercase tracking-wider mb-2 flex items-center justify-between">
                        <span>USGS Hydro Stream Ingest</span>
                        <span className="text-[10px] text-cyan-400">Total Inflow: 1,815 cfs</span>
                      </h4>
                      <div className="bg-slate-950 border border-slate-800 rounded-lg overflow-hidden text-xs font-mono">
                        <table className="w-full text-left">
                          <thead className="bg-slate-900 text-slate-400 border-b border-slate-800 text-[10px]">
                            <tr>
                              <th className="p-2">Station</th>
                              <th className="p-2">Location</th>
                              <th className="p-2 text-right">Value</th>
                            </tr>
                          </thead>
                          <tbody className="divide-y divide-slate-800/60 text-slate-300">
                            {USGS_STATIONS.map((station) => (
                              <tr key={station.id} className="hover:bg-slate-900/40 transition">
                                <td className="p-2 text-cyan-400">{station.id}</td>
                                <td className="p-2">{station.name}</td>
                                <td className="p-2 text-right font-bold text-slate-200">
                                  {station.flow ? `${station.flow} cfs` : `${station.baseElev} ft`}
                                </td>
                              </tr>
                            ))}
                          </tbody>
                        </table>
                      </div>
                    </div>

                  </div>
                </div>

                {/* TOXIC DUST DISPERSION VIEWPORT */}
                <div className="bg-slate-900 border border-slate-800 rounded-xl overflow-hidden shadow-xl flex flex-col">
                  
                  <div className="p-4 bg-slate-900 border-b border-slate-800 flex items-center justify-between">
                    <div className="flex items-center gap-2">
                      <div className="p-1.5 rounded bg-amber-500/10 text-amber-400 border border-amber-500/20">
                        <WindIcon className="w-4 h-4" />
                      </div>
                      <div>
                        <h3 className="font-bold text-sm text-slate-100 font-mono">TOXIC DUST DISPERSION VIEWPORT</h3>
                        <p className="text-[11px] text-slate-400 font-mono">Wasatch Front Air Quality Index & UDORN</p>
                      </div>
                    </div>
                    <span className="text-[10px] font-mono px-2 py-0.5 rounded bg-amber-950 text-amber-300 border border-amber-800">
                      PM10 ACTIVE
                    </span>
                  </div>

                  <div className="p-5 space-y-6 flex-1">

                    {/* Exponential Dust Power Law */}
                    <div className="bg-slate-950 border border-slate-800 rounded-lg p-4 font-mono text-xs space-y-3">
                      <div className="flex items-center justify-between text-slate-400 border-b border-slate-800 pb-2">
                        <span className="flex items-center gap-1 text-amber-400 font-semibold">
                          <ZapIcon className="w-3.5 h-3.5" /> Exponential Dust Flux Model
                        </span>
                        <span className="text-[10px]">Friction Power Law</span>
                      </div>

                      <div className="bg-slate-900 p-2.5 rounded text-center text-amber-300 text-xs tracking-wider border border-slate-800 overflow-x-auto">
                        E_dust = α · ( u_* - u_*,t )³ · e^[ β · ( 4198 - H_water ) ]
                      </div>

                      <div className="grid grid-cols-2 gap-3 pt-1">
                        <div className="p-2.5 bg-slate-900/50 rounded border border-slate-800">
                          <span className="text-slate-500 block text-[10px]">Calculated Dust Flux (E)</span>
                          <span className="text-base font-bold text-amber-400">
                            {calculations.dustFluxVal} <span className="text-slate-400 text-[10px]">mg/m²·s</span>
                          </span>
                        </div>

                        <div className="p-2.5 bg-slate-900/50 rounded border border-slate-800">
                          <span className="text-slate-500 block text-[10px]">Est. Wasatch PM10 Load</span>
                          <span className={`text-base font-bold ${calculations.estimatedPM10 > 150 ? 'text-red-400' : 'text-amber-300'}`}>
                            {calculations.estimatedPM10} <span className="text-slate-400 text-[10px]">µg/m³</span>
                          </span>
                        </div>
                      </div>
                    </div>

                    {/* Dispersion Visualizer */}
                    <div className="bg-slate-950 border border-slate-800 rounded-lg p-3 relative overflow-hidden">
                      <div className="flex items-center justify-between text-xs font-mono text-slate-400 mb-2">
                        <span className="flex items-center gap-1 text-slate-300">
                          <CompassIcon className="w-3.5 h-3.5 text-cyan-400" /> Downwind Plume Vector
                        </span>
                        <span className="text-amber-400">Vector: {windDirection} @ {windSpeed} mph</span>
                      </div>

                      <div className="h-28 bg-slate-900/90 rounded border border-slate-800 relative flex items-center justify-between px-6 overflow-hidden">
                        <div className="z-10 text-center">
                          <div className="w-10 h-10 rounded-full bg-cyan-950 border border-cyan-500/50 flex items-center justify-center text-cyan-400 font-bold text-xs shadow-lg">
                            GSL
                          </div>
                          <span className="text-[10px] text-slate-400 font-mono mt-1 block">Exposed Bed</span>
                        </div>

                        <div className="flex-1 mx-4 relative h-10 flex items-center">
                          <div 
                            className={`w-full rounded-full transition-all duration-500 ${
                              calculations.normalizedDustRisk > 60 
                                ? 'bg-gradient-to-r from-cyan-500/30 via-amber-500/60 to-red-600/80 h-8 animate-pulse' 
                                : 'bg-gradient-to-r from-cyan-500/20 via-amber-500/30 to-amber-500/40 h-4'
                            }`}
                          />
                          <span className="absolute inset-0 flex items-center justify-center text-[10px] font-mono font-bold text-amber-200">
                            PARTICULATE DUST ({calculations.normalizedDustRisk}% DENSITY)
                          </span>
                        </div>

                        <div className="z-10 text-center">
                          <div className="w-10 h-10 rounded-full bg-red-950 border border-red-500/50 flex items-center justify-center text-red-400 font-bold text-xs shadow-lg">
                            SLC
                          </div>
                          <span className="text-[10px] text-slate-400 font-mono mt-1 block">Wasatch</span>
                        </div>
                      </div>
                    </div>

                    {/* UDORN Network Grid */}
                    <div>
                      <h4 className="text-xs font-bold font-mono text-slate-300 uppercase tracking-wider mb-2 flex items-center justify-between">
                        <span>Utah Dust Oxide Research Network (UDORN)</span>
                        <span className="text-[10px] text-amber-400">5 Sensor Nodes</span>
                      </h4>
                      <div className="bg-slate-950 border border-slate-800 rounded-lg overflow-hidden text-xs font-mono">
                        <table className="w-full text-left">
                          <thead className="bg-slate-900 text-slate-400 border-b border-slate-800 text-[10px]">
                            <tr>
                              <th className="p-2">Station</th>
                              <th className="p-2">PM10 (µg/m³)</th>
                              <th className="p-2">Arsenic Factor</th>
                              <th className="p-2 text-right">Status</th>
                            </tr>
                          </thead>
                          <tbody className="divide-y divide-slate-800/60 text-slate-300">
                            {UDORN_STATIONS.map((udorn) => (
                              <tr key={udorn.id} className="hover:bg-slate-900/40 transition">
                                <td className="p-2 text-amber-300">{udorn.name}</td>
                                <td className="p-2 font-bold">{Math.round(udorn.PM10 * (calculations.normalizedDustRisk / 40))}</td>
                                <td className="p-2">{udorn.arsenicIndex}x</td>
                                <td className="p-2 text-right">
                                  <span className={`px-1.5 py-0.5 rounded text-[10px] ${
                                    udorn.status === 'Critical' 
                                      ? 'bg-red-950 text-red-400 border border-red-800' 
                                      : udorn.status === 'Warning' 
                                        ? 'bg-amber-950 text-amber-300 border border-amber-800' 
                                        : 'bg-emerald-950 text-emerald-300'
                                  }`}>
                                    {udorn.status}
                                  </span>
                                </td>
                              </tr>
                            ))}
                          </tbody>
                        </table>
                      </div>
                    </div>

                  </div>
                </div>

              </div>
            )}

            {/* EXTENDED DUST VIEW TAB */}
            {activeTab === 'dust' && (
              <div className="space-y-6">
                <div className="bg-slate-900 border border-slate-800 rounded-xl p-6 shadow-xl font-mono">
                  <h3 className="text-base font-bold text-slate-100 mb-2 flex items-center gap-2">
                    <WindIcon className="w-5 h-5 text-amber-400" />
                    UDORN HEAVY-METAL PARTICULATE MONITORING
                  </h3>
                  <p className="text-xs text-slate-400 mb-6">
                    Tracking bioavailable Arsenic, Lead, Cadmium, and Copper particles suspended in wind currents.
                  </p>

                  <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                    <div className="bg-slate-950 border border-slate-800 p-4 rounded-lg">
                      <span className="text-slate-500 text-xs block">EROSION THRESHOLD</span>
                      <span className="text-xl font-bold text-amber-400">14.0 mph (u_*,t)</span>
                      <p className="text-[11px] text-slate-400 mt-1">Wind speed required to shatter playa crust</p>
                    </div>

                    <div className="bg-slate-950 border border-slate-800 p-4 rounded-lg">
                      <span className="text-slate-500 text-xs block">ARSENIC TOXICITY MULTIPLIER</span>
                      <span className="text-xl font-bold text-red-400">{calculations.arsenicToxFactor}x Baseline</span>
                      <p className="text-[11px] text-slate-400 mt-1">Relative heavy metal concentration</p>
                    </div>

                    <div className="bg-slate-950 border border-slate-800 p-4 rounded-lg">
                      <span className="text-slate-500 text-xs block">POPULATION EXPOSURE</span>
                      <span className="text-xl font-bold text-cyan-400">2.4 Million Residents</span>
                      <p className="text-[11px] text-slate-400 mt-1">Wasatch Front urban corridor</p>
                    </div>
                  </div>

                  <div className="bg-slate-950 border border-slate-800 rounded-lg p-4">
                    <h4 className="text-xs font-bold text-slate-300 uppercase mb-3">Station PM10 Concentration Spectrum</h4>
                    <div className="space-y-3">
                      {UDORN_STATIONS.map((st) => {
                        const dynamicPM = Math.round(st.PM10 * (calculations.normalizedDustRisk / 35));
                        const pct = Math.min(100, Math.round((dynamicPM / 200) * 100));
                        return (
                          <div key={st.id} className="space-y-1">
                            <div className="flex justify-between text-xs">
                              <span className="text-slate-300">{st.name}</span>
                              <span className="text-amber-400 font-bold">{dynamicPM} µg/m³ PM10</span>
                            </div>
                            <div className="w-full bg-slate-900 h-2 rounded-full overflow-hidden">
                              <div 
                                className={`h-full rounded-full transition-all duration-500 ${
                                  pct > 70 ? 'bg-red-500' : pct > 40 ? 'bg-amber-500' : 'bg-cyan-500'
                                }`}
                                style={{ width: `${pct}%` }}
                              />
                            </div>
                          </div>
                        );
                      })}
                    </div>
                  </div>
                </div>
              </div>
            )}

            {/* x402 PROTOCOL UNIFIED PAYLOAD TERMINAL */}
            {(activeTab === 'overview' || activeTab === 'api') && (
              <div className="bg-slate-900 border border-slate-800 rounded-xl overflow-hidden shadow-xl font-mono">
                
                <div className="p-4 bg-slate-900 border-b border-slate-800 flex items-center justify-between">
                  <div className="flex items-center gap-2">
                    <div className="p-1.5 rounded bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">
                      <TerminalIcon className="w-4 h-4" />
                    </div>
                    <div>
                      <h3 className="font-bold text-sm text-slate-100">EMERGENCY PIPELINE TERMINAL</h3>
                      <p className="text-[11px] text-slate-400">x402 Protocol Unified Alert Header & Schema Broadcast</p>
                    </div>
                  </div>

                  <button
                    onClick={copyPayloadToClipboard}
                    className="bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs px-3 py-1.5 rounded border border-slate-700 flex items-center gap-1.5 transition"
                  >
                    {copylog ? <CheckCircleIcon className="w-3.5 h-3.5 text-emerald-400" /> : <DownloadIcon className="w-3.5 h-3.5 text-slate-400" />}
                    <span>{copylog ? 'Copied' : 'Copy Payload'}</span>
                  </button>
                </div>

                <div className="p-4 bg-slate-950 text-xs space-y-4">
                  <div>
                    <span className="text-slate-500 block text-[10px] uppercase mb-1">Generated x402 HTTP Response Header</span>
                    <div className="bg-slate-900 border border-slate-800 p-2.5 rounded text-emerald-400 break-all select-all">
                      {x402AlertHeader.gsl_sentinel_alert.x402_header}
                    </div>
                  </div>

                  <div>
                    <span className="text-slate-500 block text-[10px] uppercase mb-1">Standardized Broadcast JSON Payload</span>
                    <pre className="bg-slate-900 border border-slate-800 p-4 rounded-lg text-cyan-300 overflow-x-auto text-[11px] leading-relaxed">
                      {JSON.stringify(x402AlertHeader, null, 2)}
                    </pre>
                  </div>

                  <div className="flex items-center justify-between text-[11px] text-slate-400 pt-2 border-t border-slate-800">
                    <span className="flex items-center gap-1">
                      <CheckCircleIcon className="w-3.5 h-3.5 text-emerald-400" /> Wasatch Front AQI & Emergency Push Routing Active
                    </span>
                    <span>Response Latency: ~42ms</span>
                  </div>
                </div>

              </div>
            )}

            {/* PHASED LAUNCH STRATEGY ROADMAP */}
            <section className="bg-slate-900 border border-slate-800 rounded-xl p-5 font-mono shadow-xl">
              <h3 className="text-xs font-bold uppercase tracking-wider text-slate-300 mb-4 flex items-center gap-2">
                <TrendingUpIcon className="w-4 h-4 text-cyan-400" />
                Phased Launch & Verification Roadmap
              </h3>

              <div className="grid grid-cols-1 md:grid-cols-3 gap-4 text-xs">
                <div className="bg-slate-950 border border-slate-800 p-3.5 rounded-lg">
                  <div className="flex items-center justify-between mb-2">
                    <span className="text-cyan-400 font-bold">Phase 1 (M1–M2)</span>
                    <span className="text-[10px] px-1.5 py-0.5 rounded bg-cyan-950 text-cyan-300 border border-cyan-800">Complete</span>
                  </div>
                  <h4 className="font-bold text-slate-200 mb-1">Unified API Integration Layer</h4>
                  <p className="text-[11px] text-slate-400 leading-relaxed">
                    Deploy open-core web service pipelines to standardize USGS water metrics and weather datasets into low-latency JSON caches.
                  </p>
                </div>

                <div className="bg-slate-950 border border-slate-800 p-3.5 rounded-lg">
                  <div className="flex items-center justify-between mb-2">
                    <span className="text-amber-400 font-bold">Phase 2 (M3–M4)</span>
                    <span className="text-[10px] px-1.5 py-0.5 rounded bg-amber-950 text-amber-300 border border-amber-800">In Progress</span>
                  </div>
                  <h4 className="font-bold text-slate-200 mb-1">Physics-ML Validation Engine</h4>
                  <p className="text-[11px] text-slate-400 leading-relaxed">
                    Calibrate predictive dust storm algorithms by testing historical wind events against reported Wasatch Front air quality drop-offs.
                  </p>
                </div>

                <div className="bg-slate-950 border border-slate-800 p-3.5 rounded-lg">
                  <div className="flex items-center justify-between mb-2">
                    <span className="text-purple-400 font-bold">Phase 3 (M5–M6)</span>
                    <span className="text-[10px] px-1.5 py-0.5 rounded bg-slate-800 text-slate-400">Scheduled</span>
                  </div>
                  <h4 className="font-bold text-slate-200 mb-1">Emergency Webhook Launch</h4>
                  <p className="text-[11px] text-slate-400 leading-relaxed">
                    Roll out automated x402 alert systems to activate state public health notification procedures when compounding risk factors breach safety levels.
                  </p>
                </div>
              </div>
            </section>

          </main>

          {/* FOOTER */}
          <footer className="bg-slate-900 border-t border-slate-800 py-4 px-6 mt-8 font-mono text-xs text-slate-500">
            <div className="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-3">
              <div className="flex items-center gap-2">
                <span className="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                <span>GSL Sentinel Engine Active • Connected to USGS & UDORN Nodes</span>
              </div>
              <span>Target Baseline Pool: 4,198.0 ft MSL</span>
            </div>
          </footer>

        </div>
      );
    }

    // Render App Component
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>

