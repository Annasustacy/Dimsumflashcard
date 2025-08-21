# Dimsumflashcard
港式點心語言小卡
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>港式點心語言交換小卡</title>
    <!-- 引入 Tailwind CSS CDN 以便快速樣式化 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 引入 Inter 字體 -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        /* 設定網頁背景和基本排版 */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5; /* 淺灰色背景 */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh; /* 確保整個頁面至少佔滿視窗高度 */
            margin: 0;
            padding: 20px;
            box-sizing: border-box; /* 確保所有元素的盒模型一致 */
            /* 移除 overflow: hidden; 以便在內容超出時可滾動 */
        }

        /* 主內容容器 */
        .main-content-wrapper {
            display: flex;
            flex-direction: column; /* 垂直排版 */
            align-items: center;
            width: 100%;
            max-width: 500px; /* 限制整體內容最大寬度 */
            padding: 10px; /* 內容內邊距 */
            box-sizing: border-box;
        }

        /* 閃卡容器，用於 3D 翻轉效果 */
        .flashcard-container {
            perspective: 1000px; /* 設定透視效果 */
            width: 90%; /* 響應式寬度 */
            max-width: 400px; /* 桌機最大寬度 */
            height: 500px; /* 固定高度以保持卡片大小一致 */
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 2rem; /* 與按鈕組的間距 */
        }

        /* 閃卡主體樣式 */
        .flashcard {
            width: 100%;
            height: 100%;
            position: relative;
            transform-style: preserve-3d; /* 讓子元素在 3D 空間中呈現 */
            transition: transform 0.6s; /* 翻轉動畫時長 */
            border-radius: 1.5rem; /* 圓角 */
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05); /* 陰影 */
            cursor: pointer; /* 顯示可點擊的滑鼠指標 */
        }

        /* 翻轉後的樣式 */
        .flashcard.flipped {
            transform: rotateY(180deg);
        }

        /* 閃卡的正反面共用樣式 */
        .flashcard-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden; /* 翻轉時隱藏背面 */
            border-radius: 1.5rem; /* 圓角 */
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 1.5rem; /* 內邊距 */
            text-align: center;
            background-color: white;
            box-sizing: border-box;
        }

        /* 閃卡正面樣式 */
        .flashcard-front {
            z-index: 2; /* 確保正面在上面 */
        }

        /* 閃卡背面樣式 */
        .flashcard-back {
            transform: rotateY(180deg); /* 翻轉 180 度 */
            background-color: #f7fafc; /* 淺色背景 */
            border: 1px dashed #cbd5e0; /* 虛線邊框 */
            color: #2d3748; /* 深色文字 */
        }

        /* 卡片圖片樣式 */
        .card-image {
            width: 80%; /* 響應式圖片寬度 */
            max-width: 200px;
            height: auto;
            margin-bottom: 1rem;
            border-radius: 0.75rem; /* 圓角 */
            object-fit: contain; /* 確保圖片完全顯示 */
        }

        /* 拼音文字樣式 */
        .pinyin {
            color: #6366f1; /* 靛藍色 */
            font-size: 1.125rem; /* 字體大小 */
            font-weight: 600; /* 字體粗細 */
            margin-top: 0.5rem;
        }

        /* 英文文字樣式 */
        .english {
            color: #4b5563; /* 灰色 */
            font-size: 1rem; /* 字體大小 */
            margin-top: 0.25rem;
        }

        /* 特色與任務文字樣式 */
        .feature-task-text {
            font-size: 0.95rem;
            line-height: 1.5;
            color: #4a5568;
            margin-bottom: 1rem;
        }

        /* 特色與任務標題樣式 */
        .feature-title, .task-title {
            font-weight: 700; /* 字體粗細 */
            color: #2d3748;
            margin-top: 1rem;
            margin-bottom: 0.5rem;
            font-size: 1.05rem;
        }

        /* 按鈕組的樣式 */
        .button-group {
            display: flex;
            flex-direction: row; /* 水平排版 */
            justify-content: center;
            align-items: center;
            gap: 1rem; /* 按鈕間距 */
            width: 90%; /* 響應式寬度 */
            max-width: 400px; /* 最大寬度 */
            margin-bottom: 1rem; /* 與載入提示的間距 */
        }
        .button-group > button {
            flex: 1; /* 讓按鈕均勻分佈空間 */
            padding: 0.75rem 1rem; /* 調整按鈕內邊距 */
            font-size: 1rem; /* 調整字體大小 */
            white-space: nowrap; /* 防止文字換行 */
        }

        /* 載入指示器 */
        .loading-text {
            display: flex;
            align-items: center;
            font-size: 0.9rem; /* 調整字體大小 */
            margin-top: 1rem; /* 與上方按鈕組間距 */
            margin-bottom: 0.5rem; /* 與下方內容間距 */
        }

        /* 卡片提示文字樣式 */
        .card-tip {
            font-size: 0.85rem;
            color: #6b7280;
            margin-bottom: 1.5rem; /* 與卡片間距加大 */
            text-align: center;
            line-height: 1.4; /* 調整行高 */
        }

        /* designed by Anna Su 的樣式 */
        .designer-info {
            font-size: 0.8rem; /* 較小字體 */
            color: #6b7280; /* 較淺的顏色 */
            margin-top: 5px; /* 調整位置，靠近標題下方 */
            margin-bottom: 1.5rem; /* 與卡片提示的間距 */
            text-align: center;
        }

        /* Dim Sum Night 標題樣式 */
        .dimsum-slogan {
            font-size: 1.6rem; /* 稍微放大，使其更顯眼 */
            font-weight: 700; /* 更粗體 */
            color: #333; /* 更深的顏色 */
            margin-bottom: 1rem; /* 與下方 designer-info 的間距 */
            text-align: center;
            letter-spacing: 0.05em; /* 字元間距 */
        }
    </style>
</head>
<body>
    <div class="main-content-wrapper">
        <p class="dimsum-slogan">🥟 Dim Sum Night! Flash card</p>
        <p class="designer-info">designed by Anna Su</p>

        <!-- 卡片提示文字 -->
        <p class="card-tip">
            <span>點擊卡片可以翻面</span><br/>
            <span>Tap card to flip</span>
        </p>

        <div class="flashcard-container">
            <div id="flashcard" class="flashcard">
                <div class="flashcard-face flashcard-front">
                    <!-- 卡片正面內容將在此處動態載入 -->
                </div>
                <div class="flashcard-face flashcard-back">
                    <!-- 卡片背面內容將在此處動態載入 -->
                </div>
            </div>
        </div>

        <div class="button-group">
            <button id="prevBtn" class="bg-indigo-500 hover:bg-indigo-600 text-white font-bold py-3 px-4 rounded-xl shadow-lg transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-opacity-75">
                上一個 / Prev
            </button>
            <button id="nextBtn" class="bg-indigo-500 hover:bg-indigo-600 text-white font-bold py-3 px-4 rounded-xl shadow-lg transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-opacity-75">
                下一個 / Next
            </button>
        </div>

        <!-- 載入指示器 -->
        <div id="loadingIndicator" class="hidden text-gray-600 loading-text">
            <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-indigo-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            載入中... / Loading...
        </div>
    </div>

    <script>
        // 港式點心卡片數據
        const dimSumCards = [
            {
                chineseName: "燒賣",
                pinyin: "shāo mài",
                englishName: "Siu Mai",
                features: "傳統港式蒸點，內餡有豬肉和蝦仁。",
                englishFeatures: "A classic dim sum with pork and shrimp filling.",
                task: "請用中文或英文描述它的味道。",
                englishTask: "Describe its taste in Chinese or English.",
                imagePrompt: "a close-up, high-quality photograph of a single classic dim sum Siu Mai, steamed, with visible pork and whole shrimp filling, garnished with a tiny bit of orange roe or carrot, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蝦餃",
                pinyin: "xiā jiǎo",
                englishName: "Har Gow (Shrimp Dumpling)",
                features: "透明薄皮，裡面包著鮮蝦。",
                englishFeatures: "A translucent dumpling filled with shrimp.",
                task: "用另一種語言說「我想點一籠蝦餃」。",
                englishTask: "Say “I want to order one basket of Har Gow” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a single translucent steamed shrimp dumpling (Har Gow), perfectly pleated, with pink shrimp visible through the wrapper, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "小籠包",
                pinyin: "xiǎo lóng bāo",
                englishName: "Xiao Long Bao (Soup Dumpling)",
                features: "小籠蒸製，內含鮮美湯汁。",
                englishFeatures: "Steamed dumplings with rich soup inside.",
                task: "分享你最喜歡的小籠包吃法。",
                englishTask: "Share your favorite way to eat Xiao Long Bao.",
                imagePrompt: "a close-up, high-quality photograph of a single Xiao Long Bao (soup dumpling), freshly steamed, with a delicate pleat on top, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蛋撻",
                pinyin: "dàn tà",
                englishName: "Egg Tart",
                features: "酥脆外皮，香濃蛋奶餡。",
                englishFeatures: "Crispy crust with creamy egg custard.",
                task: "用英文或中文形容它的口感。",
                englishTask: "Describe its texture in English or Chinese.",
                imagePrompt: "a close-up, high-quality photograph of a single golden brown egg tart, showing its flaky, layered crust and smooth, creamy yellow custard filling, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "春捲",
                pinyin: "chūn juǎn",
                englishName: "Spring Roll",
                features: "外皮酥脆，內餡多為蔬菜或肉。",
                englishFeatures: "Crispy wrapper with vegetable or meat filling.",
                task: "用中文或英文說「我喜歡吃春捲」。",
                englishTask: "Say “I like eating spring rolls” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a single crispy deep-fried spring roll, golden brown, with a visible filling of vegetables and meat, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "腸粉",
                pinyin: "cháng fěn",
                englishName: "Rice Noodle Roll",
                features: "軟滑米皮，包入蝦仁或牛肉。",
                englishFeatures: "Silky rice rolls filled with shrimp or beef.",
                task: "請點一份腸粉，用另一種語言說出來。",
                englishTask: "Order rice noodle rolls in the other language.",
                imagePrompt: "a close-up, high-quality photograph of silky steamed rice noodle rolls (Cheung Fun), neatly folded or rolled, drizzled with light soy sauce, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "叉燒包",
                pinyin: "chā shāo bāo",
                englishName: "BBQ Pork Bun",
                features: "蓬鬆麵皮，內餡是甜鹹叉燒肉。",
                englishFeatures: "Fluffy bun filled with sweet-savory BBQ pork.",
                task: "用中文或英文形容叉燒包的味道。",
                englishTask: "Describe the taste of BBQ Pork Bun in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a single fluffy, white steamed BBQ pork bun (Char Siu Bao), slightly opened to reveal the glistening, red char siu filling, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "鳳爪",
                pinyin: "fèng zhuǎ",
                englishName: "Chicken Feet (Phoenix Claws)",
                features: "港式經典，以黑豆豉蒸煮入味。",
                englishFeatures: "Classic dim sum braised in black bean sauce.",
                task: "用另一種語言問朋友「你敢吃鳳爪嗎？」",
                englishTask: "Ask “Do you dare to eat chicken feet?” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of braised chicken feet (Feng Zhao), coated in a rich, dark black bean sauce, glistening, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蘿蔔糕",
                pinyin: "luó bo gāo",
                englishName: "Turnip Cake",
                features: "外脆內軟，帶有臘腸與蝦米香氣。",
                englishFeatures: "Crispy outside, soft inside, with sausage and dried shrimp flavor.",
                task: "分享你最喜歡的早餐點心。",
                englishTask: "Share your favorite breakfast dim sum.",
                imagePrompt: "a close-up, high-quality photograph of a pan-fried slice of turnip cake (Lo Bak Go), golden brown and crispy on the outside, with visible pieces of radish, Chinese sausage, and dried shrimp, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "芝麻球",
                pinyin: "zhī ma qiú",
                englishName: "Sesame Ball",
                features: "外皮裹滿芝麻，內餡是紅豆沙。",
                englishFeatures: "Crispy sesame-coated ball with red bean paste inside.",
                task: "用中文或英文描述它的外觀。",
                englishTask: "Describe its appearance in Chinese or English.",
                imagePrompt: "a close-up, high-quality photograph of a single golden fried sesame ball, perfectly round and coated entirely in toasted sesame seeds, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "馬拉糕",
                pinyin: "mǎ lā gāo",
                englishName: "Ma Lai Go (Steamed Sponge Cake)",
                features: "鬆軟香甜的廣式蒸蛋糕。",
                englishFeatures: "Soft and sweet Cantonese steamed sponge cake.",
                task: "用另一種語言說「我覺得這個很鬆軟」。",
                englishTask: "Say “I think this is very fluffy” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a slice of Ma Lai Go (steamed sponge cake), showing its light brown color and airy, fluffy texture, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蓮蓉包",
                pinyin: "lián róng bāo",
                englishName: "Lotus Seed Paste Bun",
                features: "綿密蓮蓉餡，香甜不膩。",
                englishFeatures: "Sweet lotus seed paste filling, smooth and rich.",
                task: "分享你吃過最甜的點心是什麼。",
                englishTask: "Share the sweetest dessert you’ve ever eaten.",
                imagePrompt: "a close-up, high-quality photograph of a single white steamed bun (Lian Rong Bao), slightly torn to reveal the smooth, light brown lotus seed paste filling, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "奶黃包",
                pinyin: "nǎi huáng bāo",
                englishName: "Custard Bun",
                features: "奶香濃郁，口感滑順。",
                englishFeatures: "Creamy custard filling with rich milk flavor.",
                task: "用另一種語言說「這個包子有奶油味」。",
                englishTask: "Say “This bun tastes like butter” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a single white steamed bun (Nai Huang Bao), slightly opened to show the creamy, yellow custard filling, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蛋黃流沙包",
                pinyin: "dàn huáng liú shā bāo",
                englishName: "Salted Egg Custard Bun",
                features: "外皮鬆軟，內餡是流心蛋黃奶黃。",
                englishFeatures: "Soft bun with molten salted egg custard filling.",
                task: "用中文或英文描述它的內餡。",
                englishTask: "Describe its filling in Chinese or English.",
                imagePrompt: "a close-up, high-quality photograph of a single soft steamed bun (Liu Sha Bao), torn open to show the rich, golden, molten salted egg yolk custard flowing out, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蠔油牛肉丸",
                pinyin: "háo yóu niú ròu wán",
                englishName: "Beef Meatball with Oyster Sauce",
                features: "牛肉鮮嫩，帶有蠔油香氣。",
                englishFeatures: "Tender beef meatball flavored with oyster sauce.",
                task: "用另一種語言說「這是牛肉做的」。",
                englishTask: "Say “This is made of beef” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of several steamed beef meatballs (Hao You Niu Rou Wan), garnished with a few sprigs of cilantro, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "魚翅灌湯餃",
                pinyin: "yú chì guàn tāng jiǎo",
                englishName: "Shark Fin Dumpling in Soup",
                features: "以高湯熬煮的大餃子，豪華版港點。",
                englishFeatures: "Large dumpling served in rich broth, a luxury dim sum.",
                task: "分享你最喜歡的湯品。",
                englishTask: "Share your favorite soup dish.",
                imagePrompt: "a close-up, high-quality photograph of a large, delicate dumpling (Guantang Jiao) served in a clear, rich broth, with a spoon gently lifting a corner, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "叉燒酥",
                pinyin: "chā shāo sū",
                englishName: "BBQ Pork Pastry",
                features: "酥皮層層疊疊，內餡是叉燒。",
                englishFeatures: "Flaky pastry filled with BBQ pork.",
                task: "用中文或英文說出「我想吃這個」。",
                englishTask: "Say “I want to eat this” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a single golden, flaky BBQ pork pastry (Char Siu Sou), showing visible layers of pastry and a glimpse of the savory pork filling, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蘿蔔絲酥餅",
                pinyin: "luó bo sī sū bǐng",
                englishName: "Radish Pastry",
                features: "外皮酥香，內餡是清爽的蘿蔔絲。",
                englishFeatures: "Crispy pastry with shredded radish filling.",
                task: "描述你最喜歡的酥皮點心。",
                englishTask: "Describe your favorite pastry snack.",
                imagePrompt: "a close-up, high-quality photograph of a crispy radish pastry (Lo Bo Si Sou Bing) with a light, golden exterior and a savory shredded radish filling, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蠔餅",
                pinyin: "háo bǐng",
                englishName: "Oyster Cake",
                features: "外酥內鮮，充滿海鮮香氣。",
                englishFeatures: "Crispy outside, savory oyster filling inside.",
                task: "用另一種語言說「我喜歡海鮮」。",
                englishTask: "Say “I like seafood” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a pan-fried oyster cake (Hao Bing) with crispy golden edges and visible fresh oysters, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "雞蛋仔",
                pinyin: "jī dàn zǎi",
                englishName: "Egg Waffle",
                features: "外脆內軟，香港街頭人氣小吃。",
                englishFeatures: "Crispy on the outside, fluffy inside, popular HK street snack.",
                task: "分享你最喜歡的街頭小吃。",
                englishTask: "Share your favorite street food.",
                imagePrompt: "a close-up, high-quality photograph of a freshly made golden egg waffle (Gai Daan Jai), with perfectly formed spherical bubbles, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "燒賣皇",
                pinyin: "shāo mài huáng",
                englishName: "King Siu Mai",
                features: "尺寸加大的燒賣，口感更豐富。",
                englishFeatures: "Bigger version of siu mai with more filling.",
                task: "用中文或英文比較它和普通燒賣的不同。",
                englishTask: "Compare it with regular siu mai in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a single larger, premium Siu Mai (King Siu Mai), with abundant pork and shrimp filling and a clear garnish on top, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "牛肉腸粉",
                pinyin: "niú ròu cháng fěn",
                englishName: "Beef Rice Noodle Roll",
                features: "滑嫩腸粉包裹鮮嫩牛肉。",
                englishFeatures: "Silky rice rolls wrapped with tender beef.",
                task: "用另一種語言說「我想要牛肉口味」。",
                englishTask: "Say “I want the beef flavor” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of silky steamed rice noodle rolls (Cheung Fun) filled with tender slices of beef, drizzled with glossy soy sauce, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "油條",
                pinyin: "yóu tiáo",
                englishName: "Fried Dough Stick",
                features: "金黃酥脆，常搭配豆漿或粥。",
                englishFeatures: "Golden and crispy, often paired with soy milk or congee.",
                task: "分享你早餐最常吃的食物。",
                englishTask: "Share what you usually eat for breakfast.",
                imagePrompt: "a close-up, high-quality photograph of a single golden brown, crispy fried dough stick (Youtiao), with a light and airy texture, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "糯米雞",
                pinyin: "nuò mǐ jī",
                englishName: "Sticky Rice with Chicken (Lo Mai Gai)",
                features: "荷葉包裹糯米、雞肉和香菇蒸製。",
                englishFeatures: "Glutinous rice steamed in lotus leaf with chicken and mushroom.",
                task: "用另一種語言說「我想要一份糯米雞」。",
                englishTask: "Say “I want one Lo Mai Gai” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a steamed sticky rice with chicken (Lo Mai Gai), still partially wrapped in its lotus leaf, showing the general shape, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "牛肉餅",
                pinyin: "niú ròu bǐng",
                englishName: "Beef Pancake",
                features: "外皮香脆，內餡多汁牛肉。",
                englishFeatures: "Crispy pancake with juicy beef filling.",
                task: "用中文或英文比較它和漢堡。",
                englishTask: "Compare it with a hamburger in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a pan-fried crispy beef pancake (Niu Rou Bing), golden brown with a savory beef filling, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "蔥油餅",
                pinyin: "cōng yóu bǐng",
                englishName: "Scallion Pancake",
                features: "香脆帶有濃濃蔥香。",
                englishFeatures: "Crispy with a strong scallion flavor.",
                task: "用另一種語言說「這個有蔥味」。",
                englishTask: "Say “This tastes like scallion” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a golden, flaky scallion pancake (Cong You Bing), showing visible chopped scallions embedded within its layers, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "咖哩角",
                pinyin: "gā lí jiǎo",
                englishName: "Curry Puff",
                features: "酥皮點心，內餡是香辣咖哩。",
                englishFeatures: "Flaky pastry with spicy curry filling.",
                task: "用中文或英文說「我覺得這個有點辣」。",
                englishTask: "Say “I think this is a bit spicy” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a single golden brown, flaky curry puff (Gali Jiao), with a distinctive crimped edge, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "薑汁撞奶",
                pinyin: "jiāng zhī zhuàng nǎi",
                englishName: "Ginger Milk Pudding",
                features: "薑汁與熱牛奶製成的滑嫩甜品。",
                englishFeatures: "Smooth dessert made with ginger juice and hot milk.",
                task: "用另一種語言描述你喜歡的甜品。",
                englishTask: "Describe your favorite dessert in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a smooth, creamy ginger milk pudding (Jiang Zhi Zhuang Nai) in a small white ceramic bowl, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "雙皮奶",
                pinyin: "shuāng pí nǎi",
                englishName: "Double Skin Milk Pudding",
                features: "奶香濃郁，滑嫩香甜。",
                englishFeatures: "Rich and creamy milk pudding with silky texture.",
                task: "用中文或英文說「這個很好吃」。",
                englishTask: "Say “This tastes delicious” in the other language.",
                imagePrompt: "a close-up, high-quality photograph of a smooth, creamy double skin milk pudding (Shuang Pi Nai) in a small white ceramic bowl, showing its two distinct layers, on a plain white studio background, isolated, professional food photography"
            },
            {
                chineseName: "芒果布丁",
                pinyin: "máng guǒ bù dīng",
                englishName: "Mango Pudding",
                features: "香甜芒果味，清爽可口。",
                englishFeatures: "Sweet mango flavor, refreshing and tasty.",
                task: "分享你最喜歡的水果甜點。",
                englishTask: "Share your favorite fruit dessert.",
                imagePrompt: "a close-up, high-quality photograph of a vibrant yellow mango pudding (Mango Buding) in a clear glass cup, topped with fresh mango cubes, on a plain white studio background, isolated, professional food photography"
            }
        ];

        let currentCardIndex = 0;
        const flashcardElement = document.getElementById('flashcard');
        const flashcardFront = flashcardElement.querySelector('.flashcard-front');
        const flashcardBack = flashcardElement.querySelector('.flashcard-back');
        const prevBtn = document.getElementById('prevBtn');
        const nextBtn = document.getElementById('nextBtn');
        const loadingIndicator = document.getElementById('loadingIndicator');

        // 顯示當前卡片內容的函數
        async function displayCard(index) {
            flashcardElement.classList.remove('flipped'); // 切換卡片時確保是正面
            loadingIndicator.classList.remove('hidden'); // 顯示載入指示器

            const cardData = dimSumCards[index];

            let imageUrl = '';
            try {
                // 呼叫 Imagen API 生成圖片
                const payload = {
                    instances: { prompt: cardData.imagePrompt },
                    parameters: { "sampleCount": 1 }
                };
                const apiKey = ""; // API 金鑰在 Canvas 環境中會自動提供
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-002:predict?key=${apiKey}`;

                let response;
                let result;
                // 實作指數退避重試機制
                for (let i = 0; i < 5; i++) { // 最多重試 5 次
                    try {
                        response = await fetch(apiUrl, {
                            method: 'POST',
                            headers: { 'Content-Type': 'application/json' },
                            body: JSON.stringify(payload)
                        });
                        result = await response.json();
                        if (response.ok) {
                            break; // 成功則跳出迴圈
                        }
                    } catch (error) {
                        // 網路錯誤或其他無法解析的錯誤
                        console.warn(`API call failed, retrying in ${2 ** i * 1000}ms...`);
                    }
                    await new Promise(resolve => setTimeout(resolve, 2 ** i * 1000)); // 指數退避延遲
                }

                if (result.predictions && result.predictions.length > 0 && result.predictions[0].bytesBase64Encoded) {
                    imageUrl = `data:image/png;base64,${result.predictions[0].bytesBase64Encoded}`;
                } else {
                    console.error("圖片生成失敗，使用預設圖片:", result);
                    // 如果圖片生成失敗，使用預設圖片作為替代
                    imageUrl = `https://placehold.co/200x150/e0e0e0/ffffff?text=${encodeURIComponent(cardData.chineseName)}圖片載入失敗`;
                }
            } catch (error) {
                console.error("生成圖片時發生錯誤:", error);
                // 如果 API 呼叫失敗，使用預設圖片作為替代
                imageUrl = `https://placehold.co/200x150/e0e0e0/ffffff?text=${encodeURIComponent(cardData.chineseName)}圖片載入失敗`;
            } finally {
                loadingIndicator.classList.add('hidden'); // 隱藏載入指示器
            }

            // 更新卡片正面的內容
            flashcardFront.innerHTML = `
                <img src="${imageUrl}" alt="${cardData.chineseName}" class="card-image rounded-lg mb-4" onerror="this.onerror=null;this.src='https://placehold.co/200x150/e0e0e0/ffffff?text=${encodeURIComponent(cardData.chineseName)}圖片載入失敗';">
                <h2 class="text-2xl sm:text-3xl font-bold text-gray-900 mb-2">${cardData.chineseName}</h2>
                <p class="pinyin">${cardData.pinyin}</p>
                <p class="english">${cardData.englishName}</p>
            `;

            // 更新卡片背面的內容
            flashcardBack.innerHTML = `
                <div class="feature-title">特色 / Features</div>
                <p class="feature-task-text">${cardData.features}<br/>${cardData.englishFeatures}</p>
                <div class="task-title">任務 / Task</div>
                <p class="feature-task-text">${cardData.task}<br/>${cardData.englishTask}</p>
            `;
        }

        // 事件監聽器：上一個按鈕
        prevBtn.addEventListener('click', () => {
            currentCardIndex = (currentCardIndex - 1 + dimSumCards.length) % dimSumCards.length;
            displayCard(currentCardIndex);
        });

        // 事件監聽器：下一個按鈕
        nextBtn.addEventListener('click', () => {
            currentCardIndex = (currentCardIndex + 1) % dimSumCards.length;
            displayCard(currentCardIndex);
        });

        // 事件監聽器：點擊卡片翻面
        flashcardElement.addEventListener('click', () => {
            flashcardElement.classList.toggle('flipped');
        });

        // 頁面載入完成後顯示第一張卡片
        document.addEventListener('DOMContentLoaded', () => {
            displayCard(currentCardIndex);
        });
    </script>
</body>
</html>
