[Calendar.html](https://github.com/user-attachments/files/28345879/Calendar.html)
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>دائرة المواسم والنجوم التفاعلية الشاملة</title>
<style>
    @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;800;900&display=swap');
    
    body { 
        background-color: #0B0F19; 
        display: flex; 
        flex-direction: column;
        align-items: center; 
        min-height: 100vh; 
        margin: 0; 
        padding: 40px 20px;
        font-family: 'Cairo', sans-serif;
        color: #ffffff;
        overflow-x: hidden;
    }
    
    /* تصميم الدائرة */
    .chart-container { 
        width: 100%; 
        max-width: 900px; 
        background: #0B0F19; 
        border-radius: 50%; 
        box-shadow: 0 0 100px rgba(0,0,0,0.9); 
        padding: 10px; 
        position: relative;
        margin-bottom: 50px;
    }
    
    svg { width: 100%; height: auto; }
    text { font-weight: 800; pointer-events: none; user-select: none; text-shadow: 1px 1px 4px rgba(0,0,0,0.9); }
    .text-light { fill: #ffffff; }
    .text-silver { fill: #e2e8f0; }
    .text-gold { fill: #FFD700; font-weight: 900; } 
    
    path { 
        stroke: #0B0F19; 
        stroke-width: 5px; 
        stroke-linejoin: round;
        transition: all 0.2s ease;
        cursor: pointer;
    }
    path:hover { filter: brightness(1.3) drop-shadow(0 0 8px rgba(255,215,0,0.5)); }

    /* النافذة المنبثقة (Tooltip) */
    #tooltip {
        position: absolute; display: none; background: rgba(15, 23, 42, 0.95);
        border: 2px solid #FFD700; color: #fff; padding: 15px; border-radius: 12px;
        font-size: 14px; pointer-events: none; box-shadow: 0 10px 30px rgba(0,0,0,0.8);
        z-index: 9999; max-width: 350px; text-align: right; line-height: 1.6;
        backdrop-filter: blur(5px);
    }
    .tt-title { font-weight: 900; color: #FFD700; font-size: 18px; margin-bottom: 8px; border-bottom: 1px solid #334155; padding-bottom: 6px; }
    .agri-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-top: 10px; font-size: 13px; }
    .agri-item span { color: #38BDF8; font-weight: bold; }
    .tt-note { font-size: 13px; color: #CBD5E1; margin-top: 10px; background: rgba(0,0,0,0.3); padding: 8px; border-radius: 6px; }

    /* لوحة التحكم والمعلومات السفلية */
    .dashboard {
        width: 100%; max-width: 1100px; display: grid; grid-template-columns: 1fr; gap: 20px;
    }
    @media (min-width: 900px) { .dashboard { grid-template-columns: 1fr 1fr; } }
    
    .panel {
        background: #111827; border: 1px solid #1F2937; border-radius: 16px;
        padding: 25px; box-shadow: 0 10px 25px rgba(0,0,0,0.5);
    }
    .panel h3 { color: #FFD700; margin-top: 0; font-weight: 900; border-bottom: 2px solid #1F2937; padding-bottom: 10px; }
    
    /* القواعد والملاحظات */
    .rules-list { list-style: none; padding: 0; margin: 0; }
    .rules-list li { margin-bottom: 12px; font-size: 15px; color: #E2E8F0; display: flex; align-items: flex-start; gap: 10px; }
    .rules-list li::before { content: "✦"; color: #38BDF8; font-size: 18px; }

    /* محرك البحث والأزرار */
    .control-group { display: flex; gap: 10px; margin-bottom: 15px; }
    input[type="text"] {
        flex: 1; padding: 12px; background: #0B0F19; border: 1px solid #374151; color: #fff;
        border-radius: 8px; font-family: 'Cairo', sans-serif; font-size: 15px;
    }
    input[type="text"]:focus { outline: none; border-color: #FFD700; }
    
    button {
        padding: 12px 20px; background: #FFD700; color: #000; border: none; font-weight: 800;
        border-radius: 8px; cursor: pointer; font-family: 'Cairo', sans-serif; transition: background 0.2s;
    }
    button:hover { background: #FBBF24; }
    .btn-blue { background: #38BDF8; color: #000; width: 100%; margin-top: 10px; }
    .btn-blue:hover { background: #0EA5E9; }

    #search-result, #hijri-result {
        background: #0B0F19; border: 1px solid #374151; border-radius: 8px; padding: 15px;
        margin-top: 15px; display: none; font-size: 14px; line-height: 1.8;
    }
    .res-title { color: #FFD700; font-weight: 800; font-size: 16px; margin-bottom: 10px; }
    .res-data { color: #A7F3D0; }
</style>
</head>
<body>

<div class="chart-container">
    <svg viewBox="0 0 2400 2400">
        <circle cx="1200" cy="1200" r="300" fill="#0B0F19" stroke="#1A233A" stroke-width="4"/>
        <g id="chart-content"></g>
    </svg>
    <div id="tooltip"></div>
</div>

<div class="dashboard">
    <div class="panel">
        <h3>ملاحظات الدائرة والقواعد الفلكية</h3>
        <ul class="rules-list">
            <li><strong>السنة النجمية:</strong> ١٤٦١ يوماً مقسمة على ٤ دورات. (٣ دورات منها ٣٦٥ يوماً، والدورة الرابعة ٣٦٦ يوماً تزيد في نجم الطرفة).</li>
            <li><strong>دخول سهيل:</strong> الدورة الأولى بعد الغروب، الدورة الثانية في منتصف الليل، الدورة الثالثة بعد شروق الشمس، الدورة الرابعة بعد الزوال.</li>
            <li><strong>السنة القمرية:</strong> ٣٥٤ يوماً. وتزيد يوماً واحداً في الدورة الرابعة (في أقرب شهر فردي لنجم الطرفة).</li>
            <li><strong>الفروقات الزمنية:</strong> فرق السنة النجمية عن السنة القمرية هو ١١ يوماً (يصل الفرق في الدورات الأربع إلى ٤٥ يوماً).</li>
            <li><strong>دورة النجوم:</strong> ٨ سنوات نجمية تعادل بالضبط ٣٣ سنة قمرية.</li>
        </ul>
    </div>

    <div class="panel">
        <h3>البحث عن الطوالع والتاريخ</h3>
        <div class="control-group">
            <input type="text" id="searchInput" placeholder="اكتب اسم النجم أو الطالع (مثال: الطرفة)...">
            <button onclick="searchStar()">بحث</button>
        </div>
        <div id="search-result"></div>

        <button class="btn-blue" onclick="getTodayInfo()">اليوم الهجري والطالع الحالي</button>
        <div id="hijri-result"></div>
    </div>
</div>

<script>
    const cx = 1200, cy = 1200;
    
    // بيانات الزراعة الافتراضية لكل الطوالع
    const defaultAgri = {
        starDesc: "سيتم التحديث", ascendant: "سيتم التحديث", cycle: "سيتم التحديث",
        crops: "يُحدث لاحقاً", seeds: "يُحدث لاحقاً", harvest: "يُحدث لاحقاً",
        fertilizer: "يُحدث لاحقاً", pesticide: "يُحدث لاحقاً", pruning: "يُحدث لاحقاً", saram: "يُحدث لاحقاً"
    };

    // إنشاء مصفوفة الأيام للدائرة الخارجية (28 مربعاً مستقلاً)
    const daysRingItems = Array.from({length: 28}, (_, i) => ({
        start: i + 1,
        end: i + 1,
        text: i === 0 ? ["١٤ يوم"] : ["١٣ يوم"],
        note: i === 0 ? "مدة طالع الطرفة (تزيد يوماً في السنة الكبيسة)." : "المدة الزمنية الفلكية الثابتة للنجم."
    }));

    const rings = [
        // حلقة الفصول
        { rIn: 300, rOut: 480, textClass: "text-light", fontSize: "42px", items: [
            { start: 2, end: 8, text: ["الخريف", "(٩٢) يوم"], color: "#C25B3E", note: "يبدأ الخريف بعد انقضاء الطرفة." }, 
            { start: 9, end: 16, text: ["الشتاء", "(٩١) يوم"], color: "#2E67A0", note: "فصل البرد الشديد وانخفاض درجات الحرارة." }, 
            { start: 17, end: 23, text: ["الربيع", "(٩١) يوم"], color: "#3C8B5D", note: "فصل الاعتدال واكتساء الأرض بالخضار." }, 
            { start: 24, end: 29, text: ["الصيف", "(٩١/٩٢) يوم"], color: "#D49A2A", note: "يشتمل الصيف الآن على جزء الطرفة الواقع في زرع سهيل." } 
        ]},
        // حلقة فترات الزراعة والرياح
        { rIn: 480, rOut: 650, color: "#1A233A", textClass: "text-silver", fontSize: "32px", items: [
            { start: 1, end: 4, text: ["زرع سهيل"], note: "فترة زراعية هامة تتزامن مع خروج نجم سهيل." }, 
            { start: 5, end: 8, text: ["زرع الوسم"], note: "أفضل أوقات الزراعة." },
            { start: 9, end: 11, text: ["زرع المربعانية"], note: "بداية المربعانية والزراعة محمية." }, 
            { start: 12, end: 13, text: ["زرع الشبط"], note: "فترة الرياح الباردة." },
            { start: 14, end: 16, text: ["زرع السعودات"], note: "تبدأ الأرض بالدفء." }, 
            { start: 17, end: 18, text: ["سوقة العتق"], note: "تحرك الماء في عود الشجر." },
            { start: 19, end: 21, text: ["كنة الربيع"], note: "توديع الربيع واشتداد الدفء." }, 
            { start: 22, end: 23, text: ["البارح الصغير"], note: "رياح ربيعية دافئة." },
            { start: 24, end: 26, text: ["البارح العود"], note: "بداية الصيف الحار." }, 
            { start: 27, end: 28, text: ["جمدة الحاتمي"], note: "نهاية الحر اللاهب." }
        ]},
        // حلقة الأنواء الرئيسية
        { rIn: 650, rOut: 850, color: "#24304D", textClass: "text-light", fontSize: "30px", items: [
            { start: 1, end: 4, text: ["سهيل", "(٥٣/٥٤) يوم"], note: "انكسار حدة الحرارة." }, 
            { start: 5, end: 8, text: ["الوسم", "(٥٢) يوم"], note: "موسم الأمطار النافعة." },
            { start: 9, end: 11, text: ["المربعانية", "(٣٩) يوم"], note: "البرودة القارسة." }, 
            { start: 12, end: 13, text: ["الشبط", "(٢٦) يوم"], note: "الرياح الباردة المتقلبة." },
            { start: 14, end: 16, text: ["العقارب", "(٣٩) يوم"], note: "مرحلة انتقالية." }, 
            { start: 17, end: 18, text: ["الحميمين", "(٢٦) يوم"], note: "بداية الدفء الحقيقي وظهور الهوام." },
            { start: 19, end: 20, text: ["الذراعين", "(٢٦) يوم"], note: "أمطار السرايات." }, 
            { start: 21, end: 23, text: ["الثريا", "(٣٩) يوم"], note: "غياب وظهور الثريا." },
            { start: 24, end: 24, text: ["التويبع", "(١٣) يوم"], note: "اشتداد السموم." }, 
            { start: 25, end: 26, text: ["الجوزاء", "(٢٦) يوم"], note: "الحرارة العالية." },
            { start: 27, end: 27, text: ["المرزم", "(١٣) يوم"], note: "نضج الرطب." }, 
            { start: 28, end: 28, text: ["الكليبين", "(١٣) يوم"], note: "الرطوبة العالية." }
        ]},
        // حلقة الطوالع (النجوم) الـ 28
        { rIn: 850, rOut: 1050, color: "#2E3D60", textClass: "text-silver", fontSize: "28px", isStarRing: true, items: [
            { start: 1, text: ["الطرفة"], note: "أول طوالع نجم سهيل." }, 
            { start: 2, text: ["الجبهة"], note: "ثاني طوالع سهيل." }, 
            { start: 3, text: ["الزبرة"], note: "ثالث طوالع سهيل." }, 
            { start: 4, text: ["الصرفة"], note: "آخر طوالع سهيل." },
            { start: 5, text: ["العواء"], note: "أول طوالع الوسم." }, 
            { start: 6, text: ["السماك"], note: "ثاني طوالع الوسم." }, 
            { start: 7, text: ["الغفر"], note: "ثالث طوالع الوسم." }, 
            { start: 8, text: ["الزبانا"], note: "آخر طوالع الوسم." },
            { start: 9, text: ["الاكليل"], note: "أول طوالع المربعانية." }, 
            { start: 10, text: ["القلب"], note: "ثاني طوالع المربعانية." }, 
            { start: 11, text: ["الشولة"], note: "آخر طوالع المربعانية." }, 
            { start: 12, text: ["النعايم"], note: "أول طوالع الشبط." },
            { start: 13, text: ["البلدة"], note: "ثاني طوالع الشبط." }, 
            { start: 14, text: ["سعد الذابح"], note: "أول طوالع العقارب." }, 
            { start: 15, text: ["سعد بلع"], note: "ثاني طوالع العقارب." }, 
            { start: 16, text: ["سعد السعود"], note: "آخر طوالع العقارب." },
            { start: 17, text: ["سعد الاخبية"], note: "أول طوالع الحميمين." }, 
            { start: 18, text: ["المقدم"], note: "ثاني طوالع الحميمين." }, 
            { start: 19, text: ["المؤخر"], note: "أول طوالع الذراعين." }, 
            { start: 20, text: ["الرشا"], note: "ثاني طوالع الذراعين." },
            { start: 21, text: ["الشرطين"], note: "أول أنواء طالع الثريا." }, 
            { start: 22, text: ["البطين"], note: "ثاني أنواء الثريا." }, 
            { start: 23, text: ["الثريا"], note: "نجم غني عن التعريف." }, 
            { start: 24, text: ["الدبران"], note: "طالع التويبع." },
            { start: 25, text: ["الهقعة"], note: "أول طوالع الجوزاء." }, 
            { start: 26, text: ["الهنعة"], note: "ثاني طوالع الجوزاء." }, 
            { start: 27, text: ["الذراع"], note: "طالع المرزم." }, 
            { start: 28, text: ["النثرة"], note: "طالع الكليبين." }
        ]},
        // حلقة الأيام الخارجية
        { rIn: 1050, rOut: 1150, color: "#13192B", textClass: "text-gold", fontSize: "26px", items: daysRingItems }
    ];

    // دمج الحقول الزراعية الفارغة مع حلقة النجوم
    rings[3].items.forEach(item => {
        item.agri = { ...defaultAgri, starDesc: item.text[0], ascendant: item.text[0] };
    });

    function polarToCartesian(r, angle) {
        let rad = (-angle - 90) * Math.PI / 180;
        return { x: cx + r * Math.cos(rad), y: cy + r * Math.sin(rad) };
    }

    let group = document.getElementById('chart-content');
    let tooltip = document.getElementById('tooltip');

    rings.forEach((ring) => {
        ring.items.forEach(item => {
            let startIdx = item.start - 1;
            let endIdx = item.end ? item.end : item.start;
            
            let s = startIdx * (360 / 28);
            let e = endIdx * (360 / 28);
            
            let mid = s + (e - s) / 2;
            let p1 = polarToCartesian(ring.rIn, s);
            let p2 = polarToCartesian(ring.rIn, e);
            let p3 = polarToCartesian(ring.rOut, e);
            let p4 = polarToCartesian(ring.rOut, s);
            
            let path = document.createElementNS("http://www.w3.org/2000/svg", "path");
            let largeArcFlag = (e - s) > 180 ? 1 : 0;
            path.setAttribute("d", `M ${p1.x} ${p1.y} A ${ring.rIn} ${ring.rIn} 0 ${largeArcFlag} 0 ${p2.x} ${p2.y} L ${p3.x} ${p3.y} A ${ring.rOut} ${ring.rOut} 0 ${largeArcFlag} 1 ${p4.x} ${p4.y} Z`);
            path.setAttribute("fill", item.color || ring.color);
            
            path.addEventListener('mouseenter', (eEv) => {
                let titleText = item.text.join(' ');
                let ttHTML = `<div class="tt-title">${titleText}</div>`;
                
                if(ring.isStarRing) {
                    ttHTML += `
                    <div class="agri-grid">
                        <div class="agri-item"><span>النجم/الطالع:</span> ${item.agri.ascendant}</div>
                        <div class="agri-item"><span>الدورة:</span> ${item.agri.cycle}</div>
                        <div class="agri-item"><span>المحاصيل:</span> ${item.agri.crops}</div>
                        <div class="agri-item"><span>البذور:</span> ${item.agri.seeds}</div>
                        <div class="agri-item"><span>الحصاد:</span> ${item.agri.harvest}</div>
                        <div class="agri-item"><span>التسميد:</span> ${item.agri.fertilizer}</div>
                        <div class="agri-item"><span>الرش:</span> ${item.agri.pesticide}</div>
                        <div class="agri-item"><span>التجنيم:</span> ${item.agri.pruning}</div>
                        <div class="agri-item" style="grid-column: span 2;"><span>الصرام:</span> ${item.agri.saram}</div>
                    </div>`;
                }
                ttHTML += `<div class="tt-note">${item.note}</div>`;
                
                tooltip.innerHTML = ttHTML;
                tooltip.style.display = 'block';
            });
            
            path.addEventListener('mousemove', (eEv) => {
                tooltip.style.left = (eEv.pageX + 20) + 'px';
                tooltip.style.top = (eEv.pageY + 10) + 'px';
            });
            
            path.addEventListener('mouseleave', () => { tooltip.style.display = 'none'; });

            group.appendChild(path);
            
            let rot = -mid; 
            rot = (rot % 360 + 360) % 360; 
            if (rot > 90 && rot < 270) { rot = (rot + 180) % 360; }
            
            let lines = item.text;
            let lineSpacing = parseInt(ring.fontSize) * 1.35; 
            let rText = (ring.rIn + ring.rOut) / 2;
            
            lines.forEach((lineText, idx) => {
                let offset = ((lines.length - 1) / 2 - idx) * lineSpacing;
                let currentR = rText + offset;
                let linePos = polarToCartesian(currentR, mid);
                
                let textElement = document.createElementNS("http://www.w3.org/2000/svg", "text");
                textElement.setAttribute("x", linePos.x);
                textElement.setAttribute("y", linePos.y);
                textElement.setAttribute("text-anchor", "middle");
                textElement.setAttribute("dominant-baseline", "middle");
                textElement.setAttribute("transform", `rotate(${rot}, ${linePos.x}, ${linePos.y})`);
                textElement.setAttribute("class", ring.textClass);
                textElement.setAttribute("font-size", ring.fontSize);
                textElement.textContent = lineText;
                
                group.appendChild(textElement);
            });
        });
    });

    // محرك البحث
    function searchStar() {
        let input = document.getElementById('searchInput').value.trim();
        let resDiv = document.getElementById('search-result');
        if(!input) { resDiv.style.display = 'none'; return; }
        
        let found = rings[3].items.find(item => item.text[0].includes(input));
        if(found) {
            resDiv.innerHTML = `
                <div class="res-title">نتائج البحث عن: ${found.text[0]}</div>
                <div><span class="res-data">الملاحظة:</span> ${found.note}</div>
                <div><span class="res-data">المحاصيل:</span> ${found.agri.crops} | <span class="res-data">الحصاد:</span> ${found.agri.harvest}</div>
                <div><span class="res-data">التسميد:</span> ${found.agri.fertilizer} | <span class="res-data">الرش:</span> ${found.agri.pesticide}</div>
            `;
            resDiv.style.display = 'block';
        } else {
            resDiv.innerHTML = `<div style="color: #F87171;">لم يتم العثور على الطالع المطابق للبحث. تأكد من الإملاء.</div>`;
            resDiv.style.display = 'block';
        }
    }

    // التاريخ الهجري والطالع
    function getTodayInfo() {
        const today = new Date();
        const hijriFormatter = new Intl.DateTimeFormat('ar-SA-u-ca-islamic', {
            day: 'numeric', month: 'long', year: 'numeric'
        });
        const hijriDate = hijriFormatter.format(today);
        
        const startOfSuhail = new Date(today.getFullYear(), 7, 24);
        let diffTime = today - startOfSuhail;
        if(diffTime < 0) {
            const startOfSuhailLastYear = new Date(today.getFullYear() - 1, 7, 24);
            diffTime = today - startOfSuhailLastYear;
        }
        
        const diffDays = Math.floor(diffTime / (1000 * 60 * 60 * 24));
        let starIndex = Math.floor(diffDays / 13) % 28;
        
        let currentStar = rings[3].items[starIndex].text[0];
        
        let resDiv = document.getElementById('hijri-result');
        resDiv.innerHTML = `
            <div class="res-title">بيانات اليوم</div>
            <div><span class="res-data">التاريخ الهجري:</span> ${hijriDate}</div>
            <div><span class="res-data">الطالع/النجم المتوقع:</span> ${currentStar} (حساب تقريبي استرشادي)</div>
            <div style="font-size: 12px; color:#9CA3AF; margin-top:8px;">* يتم حساب الطالع تقريبياً بناءً على موعد دخول سهيل (24 أغسطس).</div>
        `;
        resDiv.style.display = 'block';
    }
</script>
</body>
</html>
