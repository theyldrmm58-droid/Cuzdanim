<!DOCTYPE html>  
<html lang="tr">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">  
    <title>Cüzdanım - Kişisel Bütçe</title>  
    <!-- iOS PWA Destek Etiketleri -->  
    <meta name="apple-mobile-web-app-capable" content="yes">  
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">  
    <meta name="apple-mobile-web-app-title" content="Cüzdanım">  
    <link rel="apple-touch-icon" href="https://cdn-icons-png.flaticon.com/512/3135/3135715.png">  
    <!-- Tailwind CSS CDN -->  
    <script src="https://cdn.tailwindcss.com"></script>  
    <script>  
        tailwind.config = {  
            darkMode: 'class',  
            theme: {  
                extend: {  
                    colors: {  
                        darkBg: '#0f172a',  
                        cardBg: '#1e293b',  
                        accentGreen: '#10b981',  
                        accentRed: '#ef4444',  
                        accentBlue: '#3b82F6'  
                    }  
                }  
            }  
        }  
    </script>  
    <!-- Chart.js için CDN -->  
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>  
    <style>  
        body {  
            background-color: #0f172a;  
            color: #f8fafc;  
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;  
            -webkit-tap-highlight-color: transparent;  
        }  
        /* Gizli sekme yönetimi için */  
        .tab-content { display: none; }  
        .tab-content.active { display: block; }  
        /* Özel Scrollbar */  
        ::-webkit-scrollbar { width: 4px; height: 4px; }  
        ::-webkit-scrollbar-track { background: #0f172a; }  
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 4px; }  
    </style>  
</head>  
<body class="bg-darkBg text-slate-100 pb-24 select-none">  
  
    <!-- Üst Bilgi Barı -->  
    <header class="sticky top-0 z-40 bg-cardBg/80 backdrop-blur-md border-b border-slate-800 px-4 py-3 flex justify-between items-center">  
        <div class="flex items-center space-x-2">  
            <div class="w-8 h-8 rounded-xl bg-gradient-to-tr from-emerald-500 to-blue-600 flex items-center justify-center font-bold text-white shadow-lg">C</div>  
            <h1 class="text-lg font-bold tracking-tight">Cüzdanım</h1>  
        </div>  
        <div class="flex items-center space-x-2">  
            <button onclick="openExportModal()" class="text-xs bg-slate-800 hover:bg-slate-700 px-3 py-1.5 rounded-lg border border-slate-700 transition">Yedek / Veri</button>  
        </div>  
    </header>  
  
    <!-- Ana İçerik Alanı -->  
    <main class="max-w-md mx-auto p-4">  
  
        <!-- 1. ANA SAYFA (DASHBOARD) -->  
        <section id="tab-home" class="tab-content active space-y-4">  
            <!-- Finansal Özet Kartı -->  
            <div class="bg-gradient-to-br from-slate-8  
