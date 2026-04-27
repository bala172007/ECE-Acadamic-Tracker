```
<!DOCTYPE html>
<html lang="ta">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ECE R2024 </title>
    <style>
        :root { --locked: #ffdada; --done: #d4edda; --primary: #1a73e8; --text: #333; }
        body { font-family: 'Segoe UI', sans-serif; background: #f4f7f6; color: var(--text); padding: 15px; }
        .container { max-width: 1450px; margin: auto; background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.1); }
         /*Category Progress Cards*/
        .cat-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(140px, 1fr)); gap: 10px; margin-bottom: 25px; }
        .cat-box { background: #fff; border: 1px solid #ddd; padding: 12px; border-radius: 8px; text-align: center; border-top: 5px solid var(--primary); box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .cat-box h4 { margin: 0; font-size: 0.85em; color: #666; }
        .cat-box .cr { font-size: 1.2em; font-weight: bold; color: var(--primary); margin-top: 5px; }
        .banner { display: flex; justify-content: space-around; background: #2c3e50; color: white; padding: 20px; border-radius: 10px; margin-bottom: 20px; align-items: center; }
        .stat-item { text-align: center; }
        .stat-item span { display: block; font-size: 1.5em; font-weight: bold; }
        table { width: 100%; border-collapse: collapse; font-size: 0.82em; }
        th { background: var(--primary); color: white; padding: 12px; position: sticky; top: 0; z-index: 10; text-align: center; }
        td { padding: 10px; border: 1px solid #eee; text-align: center; vertical-align: middle; }
	td:nth-child(6) { text-align: left; }     
        .row-locked { background-color: var(--locked) !important; color: #a00; }
        .row-locked input, .row-locked select { opacity: 0.3; pointer-events: none; }
        .row-done { background-color: var(--done) !important; }
        .badge { padding: 4px 8px; border-radius: 4px; font-size: 0.75em; color: white; font-weight: bold; }
        .fc { background: #6c757d; } .sbc { background: #28a745; }
        .pre-warning { display: block; font-size: 0.8em; color: #d93025; margin-top: 4px; font-weight: bold; }
        select { padding: 4px; border-radius: 4px; border: 1px solid #ccc; width: 100%; }
        input[type="checkbox"] { transform: scale(1.3); cursor: pointer; }
	@media print {
    button, .dev-tag { display: none !important; } 
    body { background: white; padding: 0; }
    .container { box-shadow: none; border: none; width: 100%; max-width: 100%; margin: 0; padding: 0; 
    /* This ensures the header looks good in the PDF too */
    div[style*="display: flex"] { display: block !important; text-align: center; }
    h2 { margin-bottom: 20px !important; }
}
    </style>
</head>
<body>

<div class="container">
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
        <h2 style="margin: 0;">B.E. ECE - Academic Progress Report (R2024) | Developed By Bala Muralitharan</h2>
        <button onclick="downloadPDF()" style="padding: 10px 20px; background: #28a745; color: white; border: none; border-radius: 5px; cursor: pointer; font-weight: bold; display: flex; align-items: center; gap: 8px; shadow: 0 2px 5px rgba(0,0,0,0.1);">
            📄 Download PDF Report
        </button>
    </div>
    <div class="cat-grid" id="catCards"></div>
    <div class="banner">
        <div class="stat-item">TOTAL CGPA<span id="cgpa">0.00</span></div>
        <div class="stat-item">Credits Completed<span id="totalCr">0 / 164</span></div>
        <div class="stat-item">Courses Completed<span id="doneCount">0 / 93</span></div>
    </div>
    <table>
        <thead>
            <tr>
                <th>Done</th>
                <th>ID</th>
                <th>Cat</th>
                <th>Type</th>
                <th>Code</th>
                <th>Course Title (Prerequisite)</th>
                <th>Cr.</th>
                <th>Grade</th>
            </tr>
        </thead>
        <tbody id="courseBody"></tbody>
    </table>
</div>

<script>
    const gradePoints = { "O": 10, "A+": 9, "A": 8, "B+": 7, "B": 6, "C": 5, "U": 0, "N/A": 0 };
    const requirements = { "HS": 13, "BS": 21, "ES": 23, "PC": 61, "PE": 15, "OE": 12, "EEC": 16, "MC": 3 };

    const courses = [
        {id:1, cat:"HS", type:"SBC", code:"SH1106", title:"English for Engineers", cr:4, pre:null},
        {id:2, cat:"HS", type:"SBC", code:"SH1107", title:"Technical Writing and Presentation", cr:2, pre:null},
        {id:3, cat:"HS", type:"FC", code:"SH9801", title:"Organizational Behavior and Leadership", cr:3, pre:null},
        {id:4, cat:"HS", type:"FC", code:"SH7801", title:"Human Values and Professional Ethics", cr:2, pre:null},
        {id:5, cat:"HS", type:"FC", code:"SH7101", title:"Heritage of Tamils", cr:1, pre:null},
        {id:6, cat:"HS", type:"FC", code:"SH7102", title:"Tamils and Technology", cr:1, pre:null},
        {id:7, cat:"BS", type:"FC", code:"SH3216", title:"Physics for Electronics Engineering", cr:4, pre:null},
        {id:8, cat:"BS", type:"FC", code:"SH4805", title:"Chemistry of Engineering Materials", cr:3, pre:null},
        {id:9, cat:"BS", type:"FC", code:"SH2201", title:"Calculus and Matrix Algebra", cr:4, pre:null},
        {id:10, cat:"BS", type:"FC", code:"SH2211", title:"Statistics and Numerical Methods", cr:4, pre:null},
        {id:11, cat:"BS", type:"FC", code:"SH2223", title:"Linear Algebra and Probability", cr:4, pre:null},
        {id:12, cat:"BS", type:"FC", code:"SH4801", title:"Environmental Sciences and Sustainability", cr:2, pre:null},
        {id:13, cat:"ES", type:"SBC", code:"EC2301", title:"Electric Circuits and Networks", cr:4, pre:null},
        {id:14, cat:"ES", type:"SBC", code:"CS3304", title:"Fundamentals of C Programming", cr:3, pre:null},
        {id:15, cat:"ES", type:"SBC", code:"CS3305", title:"Advanced C Programming", cr:3, pre:"CS3304"},
        {id:16, cat:"ES", type:"SBC", code:"CS3408", title:"Data Structures", cr:3, pre:"CS3304"},
        {id:17, cat:"ES", type:"SBC", code:"CS3306", title:"Object Oriented Programming using C++", cr:3, pre:"CS3304"},
        {id:18, cat:"ES", type:"SBC", code:"CS3307", title:"Object Oriented Programming using Java", cr:3, pre:"CS3304"},
        {id:19, cat:"ES", type:"SBC", code:"CS3301", title:"Python Programming", cr:3, pre:null},
        {id:20, cat:"ES", type:"FC", code:"EC1302", title:"Electronic Devices", cr:4, pre:null},
        {id:21, cat:"PC", type:"FC", code:"EC1402", title:"Signals and Systems", cr:3, pre:null},
        {id:22, cat:"PC", type:"FC", code:"EC1801", title:"Digital Logic Circuits Design", cr:4, pre:null},
        {id:23, cat:"PC", type:"SBC", code:"EC1419", title:"Electronic Circuits", cr:4, pre:null},
        {id:24, cat:"PC", type:"FC", code:"EC1802", title:"Electromagnetic Theory and Transmission Lines", cr:4, pre:null},
        {id:25, cat:"PC", type:"SBC", code:"EC1421", title:"Analysis and Design of Analog ICs", cr:4, pre:"EC1302"},
        {id:26, cat:"PC", type:"SBC", code:"EC1408", title:"Microprocessor and Microcontroller", cr:4, pre:"EC1801"},
        {id:27, cat:"PC", type:"FC", code:"EC1420", title:"Analog Communication", cr:4, pre:"EC1402"},
        {id:28, cat:"PC", type:"SBC", code:"EC1406", title:"Digital Communication", cr:4, pre:"EC1420"},
        {id:29, cat:"PC", type:"SBC", code:"EC1409", title:"Discrete Time Signal Processing", cr:4, pre:"EC1402"},
        {id:30, cat:"PC", type:"SBC", code:"EC1410", title:"VLSI Design", cr:4, pre:"EC1801"},
        {id:31, cat:"PC", type:"FC", code:"EC1413", title:"Wireless Communication", cr:3, pre:"EC1406"},
        {id:32, cat:"PC", type:"SBC", code:"EC1424", title:"Embedded Systems and IoT", cr:3, pre:"EC1408"},
        {id:33, cat:"PC", type:"FC", code:"EC1823", title:"Control System Engineering", cr:3, pre:"EC1402"},
        {id:34, cat:"PC", type:"SBC", code:"EC1425", title:"Optical Communication and Network", cr:3, pre:"EC1406"},
        {id:35, cat:"PC", type:"SBC", code:"EC1418", title:"Communication Networks and Cyber Security", cr:3, pre:"EC1406"},
        {id:36, cat:"PC", type:"FC", code:"EC1411", title:"Antennas and Microwave Engineering", cr:4, pre:null},
        {id:37, cat:"PC", type:"FC", code:"EC1520", title:"Introduction to MEMS and NEMS", cr:3, pre:null},
        {id:38, cat:"PE (Hons - V3)", type:"SBC", code:"EC1526", title:"Verilog HDL", cr:3, pre:"EC1801"},
        {id:39, cat:"PE (Hons - V3)", type:"SBC", code:"EC1521", title:"System Verilog for Design and Verification", cr:3, pre:"EC1526"},
        {id:40, cat:"PE (Hons - V3)", type:"SBC", code:"EC1522", title:"Physical Design for VLSI", cr:3, pre:"EC1410"},
        {id:41, cat:"PE (Hons - V3)", type:"FC", code:"EC1822", title:"VLSI Technology", cr:3, pre:"EC1410"},
        {id:42, cat:"PE (Hons - V3)", type:"FC", code:"EC1517", title:"Low Power VLSI Design", cr:3, pre:"EC1410"},
        {id:43, cat:"PE (Hons - V3)", type:"FC", code:"EC1519", title:"Testing of VLSI Circuits", cr:3, pre:"EC1410"},
        {id:44, cat:"PE (Hons - V5)", type:"FC", code:"EC1524", title:"Natural Language Processing", cr:3, pre:"EC1409"},
        {id:45, cat:"PE (Hons - V5)", type:"FC", code:"EC1503", title: "Digital Image Processing", cr:3, pre:"EC1409"},
        {id:46, cat:"PE (Hons - V5)", type:"FC", code:"EC1803", title: "Computer Vision and Pattern Recognition", cr:3, pre:null},
        {id:47, cat:"PE (Hons - V2)", type:"SBC", code:"CS1557", title:"Software Defined Networks", cr:3, pre:null},
        {id:48, cat:"PE (Hons - V1)", type:"FC", code:"EC1804", title:"Wireless Sensor Network Design", cr:3, pre:"EC1421"},
        {id:49, cat:"PE (Hons - V4)", type:"FC", code:"EC1805", title:"Data Storage Technologies", cr:3, pre:null},
        {id:50, cat:"PE (Hons - V5)", type:"FC", code:"EC1504", title:"DSP Processors", cr:3, pre:"EC1409"},
        {id:51, cat:"PE (Hons - V6)", type:"SBC", code:"EC1806", title:"Software for Embedded System", cr:3, pre:null},
        {id:52, cat:"PE (Hons - V6)", type:"SBC", code:"EC1807", title:"Real Time Operating Systems", cr:3, pre:null},
        {id:53, cat:"PE (Hons - V6)", type:"SBC", code:"EC1808", title:"Automotive Embedded Systems", cr:3, pre:"EC1424"},
        {id:54, cat:"PE (Hons - V6)", type:"SBC", code:"EC1809", title:"IoT Processors", cr:3, pre:"EC1424"},
        {id:55, cat:"PE (Hons - V6)", type:"SBC", code:"EC1810", title:"IoT Based System Design", cr:3, pre:"EC1424"},
        {id:56, cat:"PE (Hons - V2)", type:"FC", code:"EC1811", title:"5G Communication and Networks", cr:3, pre:null},
        {id:57, cat:"PE (Hons - V1)", type: "FC", code:"EC1812", title:"5G Edge and Cloud Computing", cr:3, pre:null},
        {id:58, cat:"PE (Hons - V1)", type: "FC", code:"EC1813", title:"5G Blockchains and Security", cr:3, pre:null},
        {id:59, cat:"PE (Hons - V4)", type: "FC", code:"EC1814", title:"Smart Healthcare Systems", cr:3, pre:null},
        {id:60, cat:"PE (Hons - V4)", type: "SBC", code:"EC1815", title:"AR and VR for 5G", cr:3, pre:null},
        {id:61, cat:"PE (Hons - V2)", type: "FC", code:"EC1816", title:"6G Communication Technologies", cr:3, pre:null},
        {id:62, cat:"PE (Hons - V2)", type: "FC", code:"EC1511", title:"Satellite Communication", cr:3, pre:"EC1406"},
        {id:63, cat:"PE (Hons - V7)", type: "SBC", code:"EC1817", title:"MEMS Fabrication and Integration", cr:3, pre:null},
        {id:64, cat:"PE (Hons - V7)", type: "SBC", code:"EC1818", title:"FEA and MEMS Simulation", cr:3, pre:null},
        {id:65, cat: "PE (Hons - V2)", type: "SBC", code:"EC1525", title:"RF MEMS and MOEMS", cr:3, pre:"EC1520"},
        {id:66, cat: "PE (Hons - V7)", type: "SBC", code:"EC1819", title:"BioMEMS and Microfluidics", cr:3, pre:null},
        {id:67, cat: "PE (Hons - V7)", type: "SBC", code:"EC1820", title:"Nanoscience and Nano Technology", cr:3, pre:null},
        {id:68, cat: "PE (Hons - V7)", type: "FC", code:"EC1821", title:"Design, Testability and Applications of MEMS", cr:3, pre:null},
        {id:69, cat: "PE", type: "SBC", code:"CS3404", title:"Analysis of Algorithms", cr:3, pre:"CS3301"},
        {id:70, cat: "PE", type: "SBC", code:"CS1404", title:"Database Management System", cr:4, pre:"CS3304"},
        {id:71, cat: "PE", type: "FC", code:"CS1509", title:"Graph Theory and Applications", cr:3, pre:null},
        {id:72, cat: "PE", type: "SBC", code:"CS3410", title:"Introduction to Machine Learning", cr:3, pre:null},
        {id:73, cat: "PE", type: "FC", code:"CS3801", title:"Artificial Intelligence and Applications", cr:3, pre:null},
        {id:74, cat: "PE", type: "SBC", code:"CS1542", title:"Embedded Board Design", cr:3, pre:null},
        {id:75, cat: "PE", type: "SBC", code:"CS1543", title:"Embedded Board Manufacturing", cr:3, pre:null},
        {id:76, cat: "EEC", type: "SBC", code:"EC1701", title:"Mini Project", cr:2, pre:null},
        {id:77, cat: "EEC", type: "SBC", code:"EC1702", title:"Project Work Phase I", cr:2, pre:null},
        {id:78, cat: "EEC", type: "SBC", code:"EC1703", title:"Project Work Phase II", cr:6, pre:"EC1702"},
        {id:79, cat: "EEC", type: "SBC", code:"SH6802", title:"Start Up", cr:8, pre:null},
        {id:80, cat: "EEC", type: "SBC", code:"SH6708", title:"Career Development Skills", cr:1, pre:null},
        {id:81, cat: "EEC", type: "SBC", code:"SH6709", title:"Reasoning Ability", cr:1, pre:null},
        {id:82, cat: "EEC", type: "FC", code:"SH6710", title:"Quantitative Ability I", cr:1, pre:null},
        {id:83, cat: "EEC", type: "FC", code:"SH6711", title:"Quantitative Ability II", cr:1, pre:"SH6710"},
        {id:84, cat: "EEC", type: "SBC", code:"SH6712", title:"Employment Enrichment Skills", cr:1, pre:null},
        {id:85, cat: "EEC", type: "SBC", code:"SH6713", title:"Placement Readiness Assessment", cr:1, pre:null},
        {id:86, cat: "EEC", type: "SBC", code:"SH6707", title:"Paper Publication", cr:1, pre:null},
        {id:87.1, cat: "OE", type: "SBC", code: "OE-1", title: "Open Elective Slot I", cr: 3, pre: null },
        {id:87.2, cat: "OE", type: "SBC", code: "OE-2", title: "Open Elective Slot II", cr: 3, pre: null },
        {id:87.3, cat: "OE", type: "SBC", code: "OE-3", title: "Open Elective Slot III", cr: 2, pre: null },
        {id:87.4, cat: "OE", type: "SBC", code: "OE-4", title: "Open Elective Slot IV", cr: 4, pre: null },
	{id:87.5, cat: "OE", type: "SBC", code: "OE-5", title: "Open Elective Slot V", cr: 3, pre: null },
        {id:87.6, cat: "OE", type: "SBC", code: "OE-6", title: "Open Elective Slot VI", cr: 3, pre: null },
        {id:87.7, cat: "OE", type: "SBC", code: "OE-7", title: "Open Elective Slot VII", cr: 1, pre: null },
        {id:87.8, cat: "OE", type: "SBC", code: "OE-8", title: "Open Elective Slot VII", cr: 1, pre: null },
	{id:88, cat:"MC", type: "FC", code:"SH8801", title:"Constitutionalism and AI Policies", cr:1, pre:null},
        {id:89, cat:"MC", type: "SBC", code:"SH8802", title:"Internship", cr:2, pre:null},
        {id:90, cat:"MC", type: "SBC", code:"SH8803", title:"Entrepreneurship", cr:2, pre:null},
        {id:91, cat:"MC", type: "SBC", code:"SH6803", title:"National Service Scheme", cr:0, pre:null},
        {id:92, cat:"MC", type: "SBC", code:"SH8807", title:"National Sports Organization", cr:0, pre:null},
        {id:93, cat:"MC", type: "SBC", code:"SH8808", title:"Youth Red Cross", cr:0, pre:null}
    ];

    function render() {
        const table = document.getElementById('courseBody');
        const cards = document.getElementById('catCards');
        const saved = JSON.parse(localStorage.getItem('ece_93_final_tracker')) || {};
        
        let html = '';
        courses.forEach(c => {
            const data = saved[c.code] || { d: false, g: 'N/A' };
            // Check if Prerequisite code exists in our course list and is marked as 'Done' in storage
            const isUnlocked = !c.pre || (saved[c.pre] && saved[c.pre].d);
            const rowCls = data.d ? 'row-done' : (!isUnlocked ? 'row-locked' : '');
            
            html += `
                <tr class="${rowCls}">
                    <td><input type="checkbox" ${data.d ? 'checked' : ''} onchange="upd('${c.code}','d',this.checked)" ${!isUnlocked?'disabled':''}></td>
                    <td>${c.id}</td>
                    <td>${c.cat}</td>
                    <td><span class="badge ${c.type==='FC'?'fc':'sbc'}">${c.type}</span></td>
                    <td><b>${c.code}</b></td>
                    <td>
                        ${c.title} 
                        ${c.pre ? `<span class="pre-warning">${!isUnlocked?'🔒 ' + c.pre + ' Need to Complete':'(Pre: '+c.pre+')'}</span>`:''}
                    </td>
                    <td>${c.cr}</td>
                    <td>
                        <select onchange="upd('${c.code}','g',this.value)" ${!isUnlocked?'disabled':''}>
                            ${['N/A','O','A+','A','B+','B','C','U'].map(g => `<option value="${g}" ${data.g===g?'selected':''}>${g}</option>`).join('')}
                        </select>
                    </td>
                </tr>`;
        });
        table.innerHTML = html;

        // Statistics calculation
        let earned = 0, pts = 0, gpaCr = 0, dCnt = 0;
        let cStats = { HS:0, BS:0, ES:0, PC:0, PE:0, OE:0, EEC:0, MC:0 };
        
        courses.forEach(c => {
            const s = saved[c.code];
            if(s && s.d) {
                earned += c.cr; dCnt++;
		let mainCat = c.cat.startsWith("PE") ? "PE" : c.cat;
                if(cStats.hasOwnProperty(mainCat)) {
                    cStats[mainCat] += c.cr;
                }
	
                if(s.g !== 'N/A' && s.g !== 'U') { pts += (gradePoints[s.g]*c.cr); gpaCr += c.cr; }
            }
        });

        document.getElementById('cgpa').innerText = gpaCr > 0 ? (pts/gpaCr).toFixed(2) : "0.00";
        document.getElementById('totalCr').innerText = earned + " / 164";
        document.getElementById('doneCount').innerText = dCnt + " / 93";

        cards.innerHTML = Object.keys(requirements).map(cat => `
            <div class="cat-box">
                <h4>${cat} Category</h4>
                <div class="cr">${cStats[cat]} / ${requirements[cat]}</div>
            </div>`).join('');
    }

    function upd(code, f, v) {
        let s = JSON.parse(localStorage.getItem('ece_93_final_tracker')) || {};
        if(!s[code]) s[code] = { d: false, g: 'N/A' };
        s[code][f] = v;
        localStorage.setItem('ece_93_final_tracker', JSON.stringify(s));
        render();
    }
	function downloadPDF() {
        const originalTitle = document.title;
        // PDF சேவ் ஆகும்போது இந்த பெயரில் சேவ் ஆகும்
        document.title = "ECE_Academic_Report_Bala_M"; 
        window.print();
        document.title = originalTitle;
    }
    render();
</script>
</body>
</html>
```
