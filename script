const RUNE_DATABASE = {
    Common: { chance: 0.60, power: 0.5, list: ["Batu Kali", "Ranting Tua", "Debu Tambang", "Paku Karat", "Botol Kosong", "Koin Tipis", "Baut Longgar", "Kabel Putus"] },
    Rare: { chance: 0.25, power: 2, list: ["Batu Akik", "Kristal Biru", "Tembaga Murni", "Besi Baja", "Gigi Hiu", "Fosil Kayu", "Mata Kucing", "Zamrud Hijau"] },
    Epic: { chance: 0.10, power: 10, list: ["Intan Mentah", "Serpihan Bintang", "Jiwa Hutan", "Kristal Ungu", "Nugget Emas", "Mata Naga", "Batu Bulan", "Eksoskeleton"] },
    Legendary: { chance: 0.04, power: 50, list: ["Jantung Gunung", "Nukleus Matahari", "Relik Kuno", "Pecahan Komet", "Mahkota Raja", "Pedang Cahaya"] },
    Mythic: { chance: 0.01, power: 500, list: ["Mata Dewa", "Partikel Tuhan"] }
};

let game = {
    money: 0, baseClick: 1, baseAuto: 0, clickLvl: 0, autoLvl: 0,
    runeMult: 0, pity: 0, gachaPrice: 500, pPoints: 0,
    prestigeCount: 1, skills: [], lastSave: Date.now(), activeRune: "None"
};

function getGlobalMult() {
    return (1 + game.runeMult) * Math.pow(2, game.prestigeCount - 1);
}

function updateUI() {
    const totalMult = getGlobalMult();
    const pReq = 10000 * Math.pow(5, game.prestigeCount - 1);
    const pPerc = Math.min(100, (game.money / pReq) * 100);

    document.getElementById('money').innerText = "$" + Math.floor(game.money).toLocaleString();
    document.getElementById('prestigeFill').style.width = pPerc + "%";
    document.getElementById('prestigePercent').innerText = Math.floor(pPerc);
    document.getElementById('pPoints').innerText = game.pPoints;
    document.getElementById('basePps').innerText = game.baseAuto;
    document.getElementById('globalMultDisplay').innerText = totalMult.toFixed(1);
    document.getElementById('rebirthCount').innerText = game.prestigeCount - 1;
    document.getElementById('cCost').innerText = Math.floor(15 * Math.pow(1.6, game.clickLvl)).toLocaleString();
    document.getElementById('aCost').innerText = Math.floor(100 * Math.pow(1.8, game.autoLvl)).toLocaleString();
    document.getElementById('gCost').innerText = Math.floor(game.gachaPrice).toLocaleString();
    document.getElementById('pityCount').innerText = game.pity;
    document.getElementById('runeName').innerText = game.activeRune;
    document.getElementById('runeValDisplay').innerText = `(x${game.runeMult.toFixed(1)})`;

    document.getElementById('buyClick').disabled = game.money < Math.floor(15 * Math.pow(1.6, game.clickLvl));
    document.getElementById('buyAuto').disabled = game.money < Math.floor(100 * Math.pow(1.8, game.autoLvl));
    document.getElementById('gachaBtn').disabled = game.money < game.gachaPrice;
    document.getElementById('prestigeBtn').disabled = game.money < pReq;

    game.skills.forEach(s => document.getElementById(s).classList.add('owned'));
}

document.getElementById('clickBtn').onclick = (e) => {
    let gain = game.baseClick * getGlobalMult();
    if(game.skills.includes('skill2')) gain += (game.baseAuto * getGlobalMult() * 0.05);
    game.money += gain;
    spawnText(e.clientX, e.clientY, `+$${Math.floor(gain)}`);
    updateUI();
};

document.getElementById('buyClick').onclick = () => {
    let cost = Math.floor(15 * Math.pow(1.6, game.clickLvl));
    if(game.money >= cost) { game.money -= cost; game.baseClick += 2; game.clickLvl++; updateUI(); }
};

document.getElementById('buyAuto').onclick = () => {
    let cost = Math.floor(100 * Math.pow(1.8, game.autoLvl));
    if(game.money >= cost) { game.money -= cost; game.baseAuto += 5; game.autoLvl++; updateUI(); }
};

document.getElementById('gachaBtn').onclick = () => {
    if(game.money >= game.gachaPrice) {
        game.money -= game.gachaPrice;
        game.gachaPrice *= 1.25;
        game.pity++;
        let roll = Math.random();
        let luckMod = game.skills.includes('skill3') ? 1.5 : 1;
        let selectedTier = "Common";

        if (game.pity >= 30) {
            selectedTier = Math.random() > 0.9 ? "Mythic" : "Legendary";
            game.pity = 0;
        } else {
            let accum = 0;
            const tiers = ["Mythic", "Legendary", "Epic", "Rare", "Common"];
            for (let t of tiers) {
                let chance = RUNE_DATABASE[t].chance * (t !== "Common" ? luckMod : 1);
                accum += chance;
                if(roll <= accum) { selectedTier = t; break; }
            }
        }
        const tierData = RUNE_DATABASE[selectedTier];
        const itemName = tierData.list[Math.floor(Math.random() * tierData.list.length)];
        game.activeRune = itemName;
        game.runeMult = tierData.power * (1 + Math.random());
        document.getElementById('runeName').className = selectedTier.toLowerCase() + "-txt";
        alert(`GOT: ${itemName} (x${game.runeMult.toFixed(1)})`);
        updateUI();
    }
};

document.getElementById('prestigeBtn').onclick = () => {
    const req = 10000 * Math.pow(5, game.prestigeCount - 1);
    if(game.money >= req) {
        game.prestigeCount++;
        game.pPoints += 1;
        
        // --- PROSES RESET ---
        game.money = 0; 
        game.baseClick = 1; 
        game.baseAuto = 0;
        game.clickLvl = 0; 
        game.autoLvl = 0; 
        game.runeMult = 0; 
        game.activeRune = "None";
        game.pity = 0; // Reset pity gacha juga agar adil
        
        // FITUR BARU: Harga gacha kembali ke harga awal (normal)
        game.gachaPrice = 500; 
        
        alert("REBIRTH BERHASIL!\n- Harga Gacha kembali Normal ($500)\n- Mendapat 1 Prestige Point\n- Multiplier Global x2");
        updateUI();
    }
};

document.getElementById('buySkill').onclick = () => {
    const costs = { skill1: 1, skill2: 2, skill3: 3 };
    let next = !game.skills.includes('skill1') ? 'skill1' : !game.skills.includes('skill2') ? 'skill2' : !game.skills.includes('skill3') ? 'skill3' : null;
    if(next && game.pPoints >= costs[next]) {
        game.pPoints -= costs[next]; game.skills.push(next); updateUI();
    }
};

function spawnText(x, y, txt) {
    const div = document.createElement('div');
    div.className = 'floating-text';
    div.style.left = x + 'px'; div.style.top = y + 'px';
    div.innerText = txt;
    document.body.appendChild(div);
    setTimeout(() => div.remove(), 800);
}

setInterval(() => { game.money += (game.baseAuto * getGlobalMult()); updateUI(); }, 1000);

window.onload = () => {
    const saved = localStorage.getItem('miner_ultimate_save');
    if(saved) {
        game = JSON.parse(saved);
        let offTime = Math.floor((Date.now() - game.lastSave)/1000);
        let rate = game.skills.includes('skill1') ? 1 : 0.5;
        game.money += offTime * game.baseAuto * getGlobalMult() * rate;
    }
    renderDropTable();
    updateUI();
};

function renderDropTable() {
    let html = '<strong>Peluang Gacha:</strong><br>';
    for (const tier in RUNE_DATABASE) {
        html += `<div class="drop-item ${tier.toLowerCase()}-txt"><span>${tier}</span><span>${(RUNE_DATABASE[tier].chance * 100).toFixed(0)}%</span></div>`;
    }
    document.getElementById('dropTableContainer').innerHTML = html;
}

setInterval(() => { game.lastSave = Date.now(); localStorage.setItem('miner_ultimate_save', JSON.stringify(game)); }, 5000);