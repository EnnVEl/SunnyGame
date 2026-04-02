<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <title>Sunny Game</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            margin: 0;
            padding: 16px;
            color: #f5e7d9;
            min-height: 100vh;
            transition: background 0.3s ease;
            background-size: cover;
            background-position: center;
        }

        body.bg-default { background: #1e2a1f; }
        body.bg-forest { background: linear-gradient(135deg, #1a472a, #0e2a1a); }
        body.bg-mountains { background: linear-gradient(135deg, #2c3e50, #1a2632); }
        body.bg-space { background: linear-gradient(135deg, #0b0b2b, #1a1a3e); }
        body.bg-castle { background: linear-gradient(135deg, #4a3b2c, #2a2418); }

        .npc-card {
            background: rgba(0,0,0,0.6);
            backdrop-filter: blur(10px);
            border-radius: 24px;
            padding: 16px;
            margin-bottom: 20px;
            text-align: center;
            border: 1px solid #ffd966;
        }

        .npc-name {
            font-size: 22px;
            font-weight: bold;
            color: #ffd966;
        }

        .npc-wants {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 8px;
            flex-wrap: wrap;
        }

        .want-item {
            background: rgba(0,0,0,0.5);
            padding: 6px 12px;
            border-radius: 40px;
            font-size: 14px;
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
            margin-bottom: 24px;
        }

        .cell {
            aspect-ratio: 1 / 1;
            background: rgba(0,0,0,0.5);
            backdrop-filter: blur(5px);
            border-radius: 16px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 28px;
            cursor: pointer;
            border: 2px solid #6b4e3a;
        }

        .cell.selected {
            border: 3px solid #ffb347;
            background: rgba(0,0,0,0.7);
            transform: scale(0.97);
        }

        .inventory {
            background: rgba(0,0,0,0.6);
            backdrop-filter: blur(10px);
            border-radius: 24px;
            padding: 12px;
            margin-bottom: 20px;
        }

        .inv-title {
            font-size: 14px;
            color: #c0b9a8;
            margin-bottom: 8px;
        }

        .inv-items {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            justify-content: center;
        }

        .inv-item {
            background: rgba(0,0,0,0.5);
            padding: 6px 16px;
            border-radius: 40px;
            font-size: 18px;
        }

        .coins {
            background: #ffd966;
            color: #2c2b26;
            padding: 8px 16px;
            border-radius: 40px;
            display: inline-block;
            font-weight: bold;
            margin-bottom: 16px;
        }

        .buttons {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
            justify-content: center;
            margin-top: 12px;
        }

        button {
            border: none;
            padding: 12px 20px;
            border-radius: 60px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: 0.1s;
        }

        button:active { transform: scale(0.96); }
        button.active { background: #5e7c5a; color: white; }
        button.inactive { background: #3a3a3a; color: #888; cursor: not-allowed; opacity: 0.6; }
        .shop-btn { background: #2c6e9e; color: white; }
        .sell-btn { background: #b86f2c; color: white; }
        .reset-btn { background: #5a3e2e; color: white; }
    </style>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
</head>
<body class="bg-default">

<div class="npc-card">
    <div class="npc-name">🧙‍♂️ Волшебник Мерлин</div>
    <div class="npc-wants">
        <span class="want-item">⚔️ Меч → 100 🪙</span>
        <span class="want-item">🧪 Зелье → 50 🪙</span>
        <span class="want-item">📜 Свиток → 30 🪙</span>
    </div>
</div>

<div class="grid" id="grid"></div>

<div class="inventory">
    <div class="inv-title">📦 ИНВЕНТАРЬ (можно соединять)</div>
    <div class="inv-items" id="inventory"></div>
</div>

<div class="coins" id="coinsDisplay">💰 Монет: 0</div>

<div class="buttons">
    <button id="craftMaterialsBtn" class="active">🌿 Материалы (5🪙)</button>
    <button id="craftFoodBtn" class="inactive" disabled>🔒 Еда (500🪙)</button>
    <button id="craftArmorBtn" class="inactive" disabled>🔒 Броня (1000🪙)</button>
    <button id="craftWeaponBtn" class="inactive" disabled>🔒 Оружие (1500🪙)</button>
    <button id="craftPotionBtn" class="inactive" disabled>🔒 Зелья (2000🪙)</button>
</div>

<div class="buttons">
    <button id="shopBtn" class="shop-btn">🛒 Магазин</button>
    <button id="sellToNpcBtn" class="sell-btn">💎 Продать NPC</button>
    <button id="resetGameBtn" class="reset-btn">🔄 Сброс</button>
</div>

<script>
    const tg = window.Telegram.WebApp;
    tg.ready();
    tg.expand();

    // Предметы
    let items = {
        "trava": { name: "🌿 Трава", price: 1 },
        "brevno": { name: "🪵 Бревно", price: 2 },
        "kamen": { name: "Камень", price: 5 },
        "zhelezo": { name: "⛏️ Железо", price: 10 },
        "sok": { name: "🧃 Сок", price: 8 },
        "hleb": { name: "🍞 Хлеб", price: 15 },
        "mech": { name: "⚔️ Меч", price: 100 },
        "zelie": { name: "🧪 Зелье", price: 50 },
        "svitok": { name: "📜 Свиток", price: 30 }
    };
    let itemList = ["trava", "brevno", "kamen", "zhelezo", "sok", "hleb", "mech", "zelie", "svitok"];

    // Сетка
    let grid = [
        "trava", "trava", "brevno", "kamen",
        "sok", "trava", "brevno", "zhelezo",
        "kamen", "sok", "trava", "brevno",
        "zhelezo", "mech", "zelie", "svitok"
    ];

    let inventory = {
        "trava": 0, "brevno": 0, "kamen": 0, "zhelezo": 0,
        "sok": 0, "hleb": 0, "mech": 0, "zelie": 0, "svitok": 0
    };

    let coins = 1000; // Стартовые монеты
    let selectedIndex = null;

    // Активация кнопок
    let unlocked = {
        food: false,
        armor: false,
        weapon: false,
        potion: false
    };

    // Фоны
    let backgrounds = {
        forest: { name: "🌲 Лес", price: 100, bought: false, class: "bg-forest" },
        mountains: { name: "🏔 Горы", price: 200, bought: false, class: "bg-mountains" },
        space: { name: "🌌 Космос", price: 300, bought: false, class: "bg-space" },
        castle: { name: "🏰 Замок", price: 500, bought: false, class: "bg-castle" }
    };
    let currentBg = "bg-default";

    // Загрузка/сохранение
    function saveGame() {
        localStorage.setItem("sunnyGrid", JSON.stringify(grid));
        localStorage.setItem("sunnyInventory", JSON.stringify(inventory));
        localStorage.setItem("sunnyCoins", coins);
        localStorage.setItem("sunnyUnlocked", JSON.stringify(unlocked));
        localStorage.setItem("sunnyBackgrounds", JSON.stringify(backgrounds));
        localStorage.setItem("sunnyCurrentBg", currentBg);
    }

    function loadGame() {
        const savedGrid = localStorage.getItem("sunnyGrid");
        const savedInv = localStorage.getItem("sunnyInventory");
        const savedCoins = localStorage.getItem("sunnyCoins");
        const savedUnlocked = localStorage.getItem("sunnyUnlocked");
        const savedBgs = localStorage.getItem("sunnyBackgrounds");
        const savedCurrentBg = localStorage.getItem("sunnyCurrentBg");
        if (savedGrid) grid = JSON.parse(savedGrid);
        if (savedInv) inventory = JSON.parse(savedInv);
        if (savedCoins) coins = parseInt(savedCoins);
        if (savedUnlocked) unlocked = JSON.parse(savedUnlocked);
        if (savedBgs) backgrounds = JSON.parse(savedBgs);
        if (savedCurrentBg) {
            currentBg = savedCurrentBg;
            document.body.className = currentBg;
        }
        renderAll();
        updateButtonsState();
    }

    function updateButtonsState() {
        const foodBtn = document.getElementById("craftFoodBtn");
        const armorBtn = document.getElementById("craftArmorBtn");
        const weaponBtn = document.getElementById("craftWeaponBtn");
        const potionBtn = document.getElementById("craftPotionBtn");

        if (unlocked.food) { foodBtn.className = "active"; foodBtn.disabled = false; foodBtn.innerHTML = "🍎 Еда (10🪙)"; }
        if (unlocked.armor) { armorBtn.className = "active"; armorBtn.disabled = false; armorBtn.innerHTML = "🛡️ Броня (15🪙)"; }
        if (unlocked.weapon) { weaponBtn.className = "active"; weaponBtn.disabled = false; weaponBtn.innerHTML = "⚔️ Оружие (100🪙)"; }
        if (unlocked.potion) { potionBtn.className = "active"; potionBtn.disabled = false; potionBtn.innerHTML = "🧪 Зелья (50🪙)"; }
    }

    function renderAll() {
        renderGrid();
        renderInventory();
        document.getElementById("coinsDisplay").innerHTML = `💰 Монет: ${coins}`;
    }

    function renderGrid() {
        const container = document.getElementById("grid");
        container.innerHTML = "";
        for (let i = 0; i < grid.length; i++) {
            const itemKey = grid[i];
            const display = items[itemKey]?.name || "❓";
            const cell = document.createElement("div");
            cell.className = "cell";
            if (selectedIndex === i) cell.classList.add("selected");
            cell.textContent = display;
            cell.onclick = (function(idx) { return function() { onCellClick(idx); }; })(i);
            container.appendChild(cell);
        }
    }

    function renderInventory() {
        const container = document.getElementById("inventory");
        container.innerHTML = "";
        for (let key of itemList) {
            const count = inventory[key];
            if (count > 0) {
                const div = document.createElement("div");
                div.className = "inv-item";
                div.innerHTML = `${items[key].name} x${count}`;
                container.appendChild(div);
            }
        }
        if (container.children.length === 0) container.innerHTML = "<span style='opacity:0.6'>— пусто —</span>";
    }

    function mergeItems(aKey, bKey) {
        if (aKey !== bKey) return null;
        const mergeMap = {
            "trava": "brevno", "brevno": "kamen", "kamen": "zhelezo",
            "zhelezo": "mech", "sok": "hleb", "hleb": "zelie"
        };
        return mergeMap[aKey] || null;
    }

    function onCellClick(index) {
        if (selectedIndex === null) {
            selectedIndex = index;
            renderGrid();
            return;
        }
        const firstIdx = selectedIndex;
        const secondIdx = index;
        selectedIndex = null;
        if (firstIdx === secondIdx) { renderGrid(); return; }
        const firstItem = grid[firstIdx];
        const secondItem = grid[secondIdx];
        if (!firstItem || !secondItem) { renderGrid(); return; }
        const result = mergeItems(firstItem, secondItem);
        if (result && firstItem === secondItem) {
            grid[firstIdx] = result;
            grid[secondIdx] = result;
            coins += 2;
            saveGame();
            renderAll();
        } else { renderGrid(); }
    }

    function craftItem(itemKey, costCoins) {
        if (coins < costCoins) {
            tg.showPopup({ title: "Нет монет", message: `Нужно ${costCoins} монет`, buttons: [{type: "ok"}] });
            return false;
        }
        coins -= costCoins;
        inventory[itemKey] = (inventory[itemKey] || 0) + 1;
        saveGame();
        renderAll();
        return true;
    }

    function sellToNpc() {
        let totalEarned = 0;
        const npcBuys = { "mech": 100, "zelie": 50, "svitok": 30 };
        for (let [itemKey, value] of Object.entries(npcBuys)) {
            if (inventory[itemKey] > 0) {
                totalEarned += inventory[itemKey] * value;
                coins += inventory[itemKey] * value;
                inventory[itemKey] = 0;
            }
        }
        if (totalEarned > 0) {
            saveGame();
            renderAll();
            tg.showPopup({ title: "Продажа", message: `Вы получили ${totalEarned} монет!`, buttons: [{type: "ok"}] });
        } else {
            tg.showPopup({ title: "Нет товара", message: "У вас нет предметов для NPC (меч, зелье, свиток)", buttons: [{type: "ok"}] });
        }
    }

    function resetGame() {
        grid = ["trava", "trava", "brevno", "kamen", "sok", "trava", "brevno", "zhelezo", "kamen", "sok", "trava", "brevno", "zhelezo", "mech", "zelie", "svitok"];
        inventory = { "trava": 0, "brevno": 0, "kamen": 0, "zhelezo": 0, "sok": 0, "hleb": 0, "mech": 0, "zelie": 0, "svitok": 0 };
        coins = 1000;
        unlocked = { food: false, armor: false, weapon: false, potion: false };
        selectedIndex = null;
        saveGame();
        renderAll();
        updateButtonsState();
        tg.showPopup({ title: "Сброс", message: "Игра перезапущена. Стартовые монеты: 1000", buttons: [{type: "ok"}] });
    }

    function openShop() {
        let bgShopText = "🎨 Фоны:\n";
        for (let [key, bg] of Object.entries(backgrounds)) {
            bgShopText += `${bg.name} — ${bg.price} монет ${bg.bought ? "✅" : "❌"}\n`;
        }
        let unlockShopText = `\n🔓 Активация кнопок:\n🍎 Еда — 500 монет ${unlocked.food ? "✅" : "❌"}\n🛡️ Броня — 1000 монет ${unlocked.armor ? "✅" : "❌"}\n⚔️ Оружие — 1500 монет ${unlocked.weapon ? "✅" : "❌"}\n🧪 Зелья — 2000 монет ${unlocked.potion ? "✅" : "❌"}`;
        tg.showPopup({
            title: "🛒 Магазин",
            message: bgShopText + unlockShopText,
            buttons: [
                { id: "bg_forest", type: "callback", text: "🌲 Лес (100)" },
                { id: "bg_mountains", type: "callback", text: "🏔 Горы (200)" },
                { id: "bg_space", type: "callback", text: "🌌 Космос (300)" },
                { id: "bg_castle", type: "callback", text: "🏰 Замок (500)" },
                { id: "unlock_food", type: "callback", text: "🍎 Еда (500)" },
                { id: "unlock_armor", type: "callback", text: "🛡️ Броня (1000)" },
                { id: "unlock_weapon", type: "callback", text: "⚔️ Оружие (1500)" },
                { id: "unlock_potion", type: "callback", text: "🧪 Зелья (2000)" },
                { id: "close", type: "cancel", text: "Закрыть" }
            ]
        }, (buttonId) => {
            if (buttonId === "bg_forest") buyBackground("forest", 100);
            else if (buttonId === "bg_mountains") buyBackground("mountains", 200);
            else if (buttonId === "bg_space") buyBackground("space", 300);
            else if (buttonId === "bg_castle") buyBackground("castle", 500);
            else if (buttonId === "unlock_food") unlockFeature("food", 500);
            else if (buttonId === "unlock_armor") unlockFeature("armor", 1000);
            else if (buttonId === "unlock_weapon") unlockFeature("weapon", 1500);
            else if (buttonId === "unlock_potion") unlockFeature("potion", 2000);
        });
    }

    function buyBackground(bgKey, price) {
        if (backgrounds[bgKey].bought) {
            tg.showPopup({ title: "Уже куплено", message: "Этот фон у вас уже есть", buttons: [{type: "ok"}] });
            return;
        }
        if (coins >= price) {
            coins -= price;
            backgrounds[bgKey].bought = true;
            currentBg = backgrounds[bgKey].class;
            document.body.className = currentBg;
            saveGame();
            tg.showPopup({ title: "Куплено!", message: `Фон ${backgrounds[bgKey].name} активирован`, buttons: [{type: "ok"}] });
            renderAll();
        } else {
            tg.showPopup({ title: "Нет монет", message: `Нужно ${price} монет`, buttons: [{type: "ok"}] });
        }
    }

    function unlockFeature(feature, price) {
        if (unlocked[feature]) {
            tg.showPopup({ title: "Уже активировано", message: "Эта кнопка уже доступна", buttons: [{type: "ok"}] });
            return;
        }
        if (coins >= price) {
            coins -= price;
            unlocked[feature] = true;
            saveGame();
            updateButtonsState();
            tg.showPopup({ title: "Активировано!", message: `Кнопка доступна!`, buttons: [{type: "ok"}] });
            renderAll();
        } else {
            tg.showPopup({ title: "Нет монет", message: `Нужно ${price} монет`, buttons: [{type: "ok"}] });
        }
    }

    document.getElementById("craftMaterialsBtn").onclick = () => craftItem("trava", 5);
    document.getElementById("craftFoodBtn").onclick = () => unlocked.food && craftItem("sok", 10);
    document.getElementById("craftArmorBtn").onclick = () => unlocked.armor && craftItem("kamen", 15);
    document.getElementById("craftWeaponBtn").onclick = () => unlocked.weapon && craftItem("mech", 100);
    document.getElementById("craftPotionBtn").onclick = () => unlocked.potion && craftItem("zelie", 50);
    document.getElementById("shopBtn").onclick = () => openShop();
    document.getElementById("sellToNpcBtn").onclick = () => sellToNpc();
    document.getElementById("resetGameBtn").onclick = () => resetGame();

    loadGame();
</script>
</body>
</html>
