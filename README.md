<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Skyzwrnet | 加密货币监控系统</title>
    <link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=DM+Sans:wght@300;400;500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #F7931A;
            --secondary: #627EEA;
            --bg-dark: #0a0a0f;
            --bg-card: #141418;
            --border-color: #2a2a35;
            --text-primary: #e8e8e8;
            --text-secondary: #888;
            --glow: rgba(247, 147, 26, 0.4);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'DM Sans', sans-serif;
            background: var(--bg-dark);
            color: var(--text-primary);
            min-height: 100vh;
            overflow-x: hidden;
            position: relative;
        }

        /* 动态背景网格 */
        .grid-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-image: 
                linear-gradient(rgba(247, 147, 26, 0.03) 1px, transparent 1px),
                linear-gradient(90deg, rgba(247, 147, 26, 0.03) 1px, transparent 1px);
            background-size: 50px 50px;
            animation: gridMove 20s linear infinite;
            pointer-events: none;
            z-index: 0;
        }

        @keyframes gridMove {
            0% { transform: translate(0, 0); }
            100% { transform: translate(50px, 50px); }
        }

        /* 粒子效果 */
        .particles {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 1;
        }

        .particle {
            position: absolute;
            width: 4px;
            height: 4px;
            background: var(--primary);
            border-radius: 50%;
            opacity: 0.3;
            animation: float 15s infinite;
        }

        @keyframes float {
            0%, 100% {
                transform: translateY(100vh) rotate(0deg);
                opacity: 0;
            }
            10% {
                opacity: 0.3;
            }
            90% {
                opacity: 0.3;
            }
            100% {
                transform: translateY(-100vh) rotate(720deg);
                opacity: 0;
            }
        }

        /* 主容器 */
        .container {
            position: relative;
            z-index: 10;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 20px;
            text-align: center;
        }

        /* Logo 动画 */
        .logo {
            width: 80px;
            height: 80px;
            background: linear-gradient(135deg, var(--primary), var(--secondary));
            border-radius: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 36px;
            margin-bottom: 30px;
            box-shadow: 0 0 40px var(--glow);
            animation: pulse 2s ease-in-out infinite;
            position: relative;
            overflow: hidden;
        }

        .logo::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(
                to bottom right,
                rgba(255,255,255,0) 0%,
                rgba(255,255,255,0.1) 50%,
                rgba(255,255,255,0) 100%
            );
            transform: rotate(45deg);
            animation: shine 3s infinite;
        }

        @keyframes shine {
            0% { transform: translateX(-100%) rotate(45deg); }
            100% { transform: translateX(100%) rotate(45deg); }
        }

        @keyframes pulse {
            0%, 100% { 
                box-shadow: 0 0 40px var(--glow);
                transform: scale(1);
            }
            50% { 
                box-shadow: 0 0 60px var(--glow);
                transform: scale(1.05);
            }
        }

        /* 标题样式 */
        .title {
            font-size: 48px;
            font-weight: 700;
            color: #fff;
            margin-bottom: 10px;
            letter-spacing: -0.02em;
            background: linear-gradient(135deg, #fff, #aaa);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .subtitle {
            font-size: 18px;
            color: var(--text-secondary);
            margin-bottom: 50px;
            font-weight: 400;
        }

        /* 按钮样式 */
        .enter-btn {
            position: relative;
            padding: 20px 60px;
            font-size: 18px;
            font-weight: 600;
            color: #fff;
            background: linear-gradient(135deg, var(--primary), var(--secondary));
            border: none;
            border-radius: 12px;
            cursor: pointer;
            overflow: hidden;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 0.1em;
            box-shadow: 0 10px 40px rgba(247, 147, 26, 0.3);
            text-decoration: none;
            display: inline-block;
        }

        .enter-btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(
                90deg,
                transparent,
                rgba(255,255,255,0.2),
                transparent
            );
            transition: left 0.5s ease;
        }

        .enter-btn:hover::before {
            left: 100%;
        }

        .enter-btn:hover {
            transform: translateY(-3px) scale(1.05);
            box-shadow: 0 20px 60px rgba(247, 147, 26, 0.5);
        }

        .enter-btn:active {
            transform: translateY(0) scale(0.98);
        }

        /* 状态指示器 */
        .status {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-top: 40px;
            padding: 15px 25px;
            background: var(--bg-card);
            border: 1px solid var(--border-color);
            border-radius: 10px;
        }

        .status-dot {
            width: 10px;
            height: 10px;
            background: #00ff88;
            border-radius: 50%;
            animation: blink 1.5s infinite;
        }

        @keyframes blink {
            0%, 100% { opacity: 1; box-shadow: 0 0 10px #00ff88; }
            50% { opacity: 0.5; }
        }

        .status-text {
            font-family: 'Space Mono', monospace;
            font-size: 14px;
            color: var(--text-secondary);
        }

        /* 底部信息 */
        .footer {
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            font-size: 12px;
            color: #444;
            font-family: 'Space Mono', monospace;
        }

        /* 响应式设计 */
        @media (max-width: 768px) {
            .title {
                font-size: 32px;
            }
            
            .subtitle {
                font-size: 16px;
            }
            
            .enter-btn {
                padding: 16px 40px;
                font-size: 16px;
            }
        }

        /* 加载动画 */
        .loading-bar {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 3px;
            background: var(--bg-card);
            z-index: 1000;
        }

        .loading-bar::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            height: 100%;
            background: linear-gradient(90deg, var(--primary), var(--secondary));
            animation: loading 2s ease-in-out infinite;
        }

        @keyframes loading {
            0% { width: 0%; left: 0; }
            50% { width: 30%; left: 35%; }
            100% { width: 0%; left: 100%; }
        }
    </style>
</head>
<body>
    <!-- 加载条 -->
    <div class="loading-bar"></div>
    
    <!-- 背景网格 -->
    <div class="grid-bg"></div>
    
    <!-- 粒子效果 -->
    <div class="particles" id="particles"></div>

    <!-- 主内容 -->
    <div class="container">
        <!-- Logo -->
        <div class="logo">
            ₿
        </div>
        
        <!-- 标题 -->
        <h1 class="title">Skyzwrnet</h1>
        <p class="subtitle">加密货币实时监控系统 · Crypto Monitor</p>
        
        <!-- 进入按钮 -->
        <a href="crypto-monitor.html" class="enter-btn" id="enterBtn">
            进入系统
        </a>
        
        <!-- 状态 -->
        <div class="status">
            <div class="status-dot"></div>
            <span class="status-text">SYSTEM READY · CONNECTED</span>
        </div>
    </div>

    <!-- 底部 -->
    <div class="footer">
        © 2024 Skyzwrnet | v1.0.0 | Made with ❤️
    </div>

    <script>
        // 创建粒子
        function createParticles() {
            const particlesContainer = document.getElementById('particles');
            const particleCount = 30;
            
            for (let i = 0; i < particleCount; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                particle.style.left = Math.random() * 100 + '%';
                particle.style.animationDelay = Math.random() * 15 + 's';
                particle.style.animationDuration = (Math.random() * 10 + 10) + 's';
                particlesContainer.appendChild(particle);
            }
        }

        // 按钮点击效果
        document.getElementById('enterBtn').addEventListener('click', function(e) {
            // 创建点击波纹效果
            const ripple = document.createElement('span');
            ripple.style.cssText = `
                position: absolute;
                background: rgba(255,255,255,0.5);
                border-radius: 50%;
                transform: scale(0);
                animation: ripple 0.6s linear;
                pointer-events: none;
            `;
            
            const rect = this.getBoundingClientRect();
            const size = Math.max(rect.width, rect.height);
            ripple.style.width = ripple.style.height = size + 'px';
            ripple.style.left = (e.clientX - rect.left - size/2) + 'px';
            ripple.style.top = (e.clientY - rect.top - size/2) + 'px';
            
            this.appendChild(ripple);
            
            setTimeout(() => ripple.remove(), 600);
        });

        // 添加波纹动画
        const style = document.createElement('style');
        style.textContent = `
            @keyframes ripple {
                to {
                    transform: scale(4);
                    opacity: 0;
                }
            }
        `;
        document.head.appendChild(style);

        // 初始化
        createParticles();

        // 控制台彩蛋
        console.log('%c🚀 Skyzwrnet Crypto Monitor', 'font-size: 20px; color: #F7931A; font-weight: bold;');
        console.log('%cWelcome to the future of crypto monitoring', 'font-size: 12px; color: #666;');
    </script>
</body>
</html>
