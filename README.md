# Sourdough
for calculating time for sourdough 
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Sourdough Planner</title>
    
    <!-- นำเข้าฟอนต์และไอคอน -->
    <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@300;400;500;600&family=Playfair+Display:wght@600&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        /* ตัวแปรสีสำหรับการปรับแต่ง (CSS Variables) */
        :root {
            --bg-color: #FDFBF7;
            --primary-green: #2F5A41;
            --primary-green-hover: #244632;
            --text-main: #333333;
            --text-light: #888888;
            --card-bg: #FFFFFF;
            --border-color: #EFECE5;
            --nav-bg: #FDFBF7;
        }

        * { 
            box-sizing: border-box; 
            margin: 0; 
            padding: 0; 
            font-family: 'Kanit', sans-serif; 
            -webkit-tap-highlight-color: transparent; 
        }
        
        body { background-color: #e0e0e0; display: flex; justify-content: center; }

        /* คอนเทนเนอร์หลักที่จำลองหน้าจอมือถือ */
        .app-container {
            width: 100%; max-width: 414px; height: 100vh; max-height: 896px;
            background-color: var(--bg-color); position: relative; overflow: hidden;
            box-shadow: 0 0 20px rgba(0,0,0,0.1);
        }

        /* ระบบสลับหน้าจอ (Screen Transitions) */
        .screen {
            position: absolute; top: 0; left: 0; width: 100%; height: calc(100% - 70px);
            overflow-y: auto; padding: 20px; transition: transform 0.3s ease-in-out;
            background-color: var(--bg-color); z-index: 1; padding-bottom: 40px;
        }
        .screen.hidden-right { transform: translateX(100%); z-index: 0; }
        .screen.hidden-left { transform: translateX(-100%); z-index: 0; }
        .screen.active { transform: translateX(0); z-index: 2; }

        /* ส่วนหัว (Header) */
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; padding-top: 10px; }
        .header i { font-size: 20px; color: var(--text-main); cursor: pointer; }

        /* ข้อความหลัก */
        .hero-title { font-family: 'Playfair Display', serif; font-size: 38px; line-height: 1.1; color: var(--primary-green); margin-bottom: 10px; }
        .hero-subtitle { font-size: 14px; color: var(--text-light); margin-bottom: 30px; max-width: 60%; }
        .hero-section { position: relative; }
        
        /* รูปภาพตกแต่ง */
        .bread-img {
            position: absolute; right: -20px; top: 0; width: 150px; height: 150px;
            background-image: url('https://images.unsplash.com/photo-1585478259715-876a6a81fa08?auto=format&fit=crop&w=300&q=80');
            background-size: cover; background-position: center; border-radius: 50%; box-shadow: 0 10px 20px rgba(0,0,0,0.1);
        }

        .section-title { font-size: 16px; font-weight: 500; margin-bottom: 15px; color: var(--text-main); }
        .settings-card { background-color: var(--card-bg); border-radius: 20px; padding: 10px 20px; box-shadow: 0 4px 15px rgba(0,0,0,0.03); margin-bottom: 30px; }
        .setting-item { display: flex; justify-content: space-between; align-items: center; padding: 15px 0; border-bottom: 1px solid var(--border-color); }
        .setting-item:last-child { border-bottom: none; }
        .setting-label { display: flex; align-items: center; gap: 12px; font-size: 15px; color: var(--text-main); }
        .setting-label i { color: var(--text-light); width: 20px; text-align: center; }
        
        /* ฟอร์ม Input ต่างๆ */
        .setting-value { display: flex; align-items: center; gap: 10px; color: var(--text-main); font-weight: 500; font-size: 15px; }
        .inline-input { width: 45px; border: none; text-align: right; font-family: inherit; font-size: 16px; font-weight: 600; color: var(--primary-green); background: rgba(47, 90, 65, 0.05); padding: 2px 5px; border-radius: 5px; outline: none; }
        .time-input-group { display: flex; align-items: center; background: rgba(47, 90, 65, 0.05); border-radius: 8px; padding: 2px 5px; }
        .time-adjust-btn { cursor: pointer; padding: 5px 8px; color: var(--primary-green); font-size: 14px; }
        .time-input-inline { border: none; background: transparent; font-size: 16px; font-family: inherit; font-weight: 600; color: var(--primary-green); outline: none; text-align: center; width: 65px; }

        /* ปุ่มกดหลัก */
        .btn-primary { width: 100%; background-color: var(--primary-green); color: white; border: none; padding: 16px; border-radius: 12px; font-size: 16px; font-weight: 500; display: flex; justify-content: center; align-items: center; gap: 10px; cursor: pointer; box-shadow: 0 4px 15px rgba(47, 90, 65, 0.3); }

        /* หน้า Timeline */
        .timeline-summary { display: flex; justify-content: space-between; background-color: var(--card-bg); padding: 15px 20px; border-radius: 15px; margin-bottom: 25px; box-shadow: 0 4px 15px rgba(0,0,0,0.03); }
        .summary-item { display: flex; flex-direction: column; gap: 5px; }
        .summary-label { font-size: 12px; color: var(--text-light); }
        .summary-val { font-size: 14px; font-weight: 500; color: var(--text-main); }

        .timeline-container { position: relative; padding-left: 25px; }
        .timeline-container::before { content: ''; position: absolute; left: 36px; top: 20px; bottom: 40px; width: 2px; background-color: var(--primary-green); z-index: 1; }
        .timeline-item { display: flex; align-items: center; margin-bottom: 25px; position: relative; z-index: 2; }
        .timeline-icon { width: 24px; height: 24px; background-color: var(--bg-color); border: 2px solid var(--primary-green); border-radius: 50%; display: flex; justify-content: center; align-items: center; margin-right: 15px; color: var(--primary-green); font-size: 10px; }
        .timeline-icon.filled { background-color: var(--primary-green); color: white; }
        .timeline-content { flex: 1; display: flex; justify-content: space-between; align-items: center; background-color: var(--card-bg); padding: 12px 15px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.02); }
        .step-name { font-size: 14px; font-weight: 500; color: var(--text-main); }
        .step-desc { font-size: 12px; color: var(--text-light); margin-top: 2px; }
        .step-time { font-size: 14px; font-weight: 600; color: var(--text-main); }

        .btn-outline { width: 100%; background-color: transparent; color: var(--primary-green); border: 1px solid var(--primary-green); padding: 12px; border-radius: 12px; font-size: 14px; font-weight: 500; display: flex; justify-content: center; align-items: center; gap: 8px; margin-top: 10px; margin-bottom: 20px; cursor: pointer; }

        /* หน้า Graph */
        .temp-tabs { display: flex; gap: 10px; margin-bottom: 20px; overflow-x: auto; padding-bottom: 5px; }
        .tab { padding: 8px 15px; border-radius: 10px; background: #EFECE5; color: var(--text-main); font-size: 14px; font-weight: 500; cursor: pointer; white-space: nowrap; transition: 0.2s; }
        .tab.active { background: var(--primary-green); color: white; }
        
        .graph-card { background: white; border-radius: 20px; padding: 20px; margin-bottom: 20px; box-shadow: 0 4px 15px rgba(0,0,0,0.03); }
        canvas { width: 100%; height: 200px; }

        .detail-row { display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid var(--border-color); font-size: 14px; }
        .detail-row:last-child { border-bottom: none; }
        .detail-label { color: var(--text-light); }
        .detail-val { font-weight: 500; color: var(--text-main); }

        /* แถบเมนูด้านล่าง (Bottom Navigation) */
        .bottom-nav { position: absolute; bottom: 0; left: 0; width: 100%; height: 70px; background-color: var(--nav-bg); border-top: 1px solid var(--border-color); display: flex; justify-content: space-around; align-items: center; padding-bottom: 15px; z-index: 10; }
        .nav-item { display: flex; flex-direction: column; align-items: center; gap: 5px; color: var(--text-light); cursor: pointer; text-decoration: none; }
        .nav-item.active { color: var(--primary-green); }
        .nav-item i { font-size: 20px; }
        .nav-item span { font-size: 10px; font-weight: 500; }
    </style>
</head>
<body>

    <div class="app-container">
        
        <!-- Screen 1: หน้าคำนวณหลัก -->
        <div id="screen-calculator" class="screen active">
            <div class="header">
                <i class="fa-solid fa-bars"></i>
                <i class="fa-regular fa-clock"></i>
            </div>
            
            <div class="hero-section">
                <h1 class="hero-title">Sourdough<br>Calculator</h1>
                <p class="hero-subtitle">คำนวณเวลาอย่างแม่นยำ</p>
                <div class="bread-img"></div>
            </div>
            
            <h2 class="section-title">ตั้งค่าการหมัก</h2>
            <div class="settings-card">
                <div class="setting-item">
                    <div class="setting-label"><i class="fa-regular fa-clock"></i> เริ่มต้น</div>
                    <div class="setting-value">
                        วันนี้, 
                        <div class="time-input-group">
                            <i class="fa-solid fa-minus time-adjust-btn" onclick="adjustTime(-15)"></i>
                            <input type="time" id="input-time" value="08:00" class="time-input-inline">
                            <i class="fa-solid fa-plus time-adjust-btn" onclick="adjustTime(15)"></i>
                        </div>
                    </div>
                </div>
                <div class="setting-item">
                    <div class="setting-label"><i class="fa-solid fa-temperature-half"></i> อุณหภูมิห้อง</div>
                    <div class="setting-value"><input type="number" id="input-temp" class="inline-input" value="27"> °C</div>
                </div>
                <div class="setting-item">
                    <div class="setting-label"><i class="fa-solid fa-jar"></i> Starter</div>
                    <div class="setting-value"><input type="number" id="input-starter" class="inline-input" value="20"> %</div>
                </div>
                <div class="setting-item">
                    <div class="setting-label"><i class="fa-solid fa-droplet"></i> Hydration</div>
                    <div class="setting-value"><input type="number" id="input-hydration" class="inline-input" value="70"> %</div>
                </div>
            </div>
            
            <button class="btn-primary" onclick="calculateAndGoToTimeline()">
                <i class="fa-solid fa-calculator"></i> คำนวณตารางเวลา
            </button>
        </div>

        <!-- Screen 2: หน้าตารางเวลา (Timeline) -->
        <div id="screen-timeline" class="screen hidden-right">
            <div class="header">
                <i class="fa-solid fa-arrow-left" onclick="showScreen('calculator', 'left')"></i>
                <h2 class="section-title" style="margin:0;">แผนการทำ</h2>
                <i class="fa-regular fa-pen-to-square"></i>
            </div>
            
            <div class="timeline-summary">
                <div class="summary-item"><span class="summary-label">เริ่ม</span><span class="summary-val" id="sum-time">08:00</span></div>
                <div class="summary-item"><span class="summary-label">อุณหภูมิ</span><span class="summary-val" id="sum-temp">27°C</span></div>
                <div class="summary-item"><span class="summary-label">Starter</span><span class="summary-val" id="sum-starter">20%</span></div>
            </div>
            
            <!-- ส่วนที่จะถูกเพิ่มเนื้อหาด้วย JavaScript -->
            <div class="timeline-container" id="timeline-list"></div>
            
            <button class="btn-outline" onclick="drawGraphAndShow()">
                <i class="fa-solid fa-chart-line"></i> ดูกราฟการเติบโต
            </button>
        </div>

        <!-- Screen 3: หน้ากราฟวิเคราะห์ (Canvas) -->
        <div id="screen-graph" class="screen hidden-right">
            <div class="header">
                <i class="fa-solid fa-arrow-left" onclick="showScreen('timeline', 'left')"></i>
                <h2 class="section-title" style="margin:0;">กราฟการหมัก</h2>
                <i class="fa-solid fa-share-nodes"></i>
            </div>
            
            <div class="temp-tabs">
                <div class="tab active" onclick="updateGraphByTemp(27, this)">27°C (ปัจจุบัน)</div>
                <div class="tab" onclick="updateGraphByTemp(25, this)">25°C</div>
                <div class="tab" onclick="updateGraphByTemp(24, this)">24°C</div>
            </div>
            
            <div class="graph-card">
                <p style="font-size:12px; color:var(--text-light); margin-bottom:10px;">
                    ระยะเวลา Bulk Fermentation: <b id="graph-bulk-label" style="color:var(--primary-green)">-</b>
                </p>
                <canvas id="bulkChart"></canvas>
            </div>
            
            <div class="settings-card" style="padding:10px 20px;">
                <div class="detail-row"><span class="detail-label">อุณหภูมิที่เลือกจำลอง</span><span class="detail-val" id="det-temp">27°C</span></div>
                <div class="detail-row"><span class="detail-label">เวลา Bulk โดยประมาณ</span><span class="detail-val" id="det-bulk" style="color:var(--primary-green); font-weight:600;">-</span></div>
                <div class="detail-row"><span class="detail-label">Starter / Hydration</span><span class="detail-val"><span id="det-starter">20%</span> / <span id="det-hydration">70%</span></span></div>
            </div>
        </div>

        <!-- เมนูด้านล่างสุด -->
        <nav class="bottom-nav">
            <div class="nav-item active" onclick="showScreen('calculator', 'left')"><i class="fa-solid fa-calculator"></i><span>คำนวณ</span></div>
            <div class="nav-item"><i class="fa-solid fa-book"></i><span>สูตร</span></div>
            <div class="nav-item"><i class="fa-regular fa-clipboard"></i><span>บันทึก</span></div>
            <div class="nav-item"><i class="fa-regular fa-lightbulb"></i><span>ความรู้</span></div>
        </nav>
        
    </div>

    <script>
        // สถานะปัจจุบันของการตั้งค่า
        let currentConfig = { temp: 27, starter: 20, hydration: 70 };

        // 1. ฟังก์ชันจัดการการสลับหน้าจอ (UI Slider)
        function showScreen(screenId, direction) {
            document.querySelectorAll('.screen').forEach(s => {
                s.classList.remove('active', 'hidden-left', 'hidden-right');
                if (s.id !== 'screen-' + screenId) {
                    s.classList.add(direction === 'right' ? 'hidden-left' : 'hidden-right');
                }
            });
            document.getElementById('screen-' + screenId).classList.add('active');
        }

        // 2. ฟังก์ชันปรับเวลาจากปุ่ม +/-
        function adjustTime(mins) {
            const input = document.getElementById('input-time');
            let [h, m] = input.value.split(':').map(Number);
            let d = new Date(); 
            d.setHours(h, m + mins);
            input.value = d.toTimeString().slice(0, 5);
        }

        // 3. ฟังก์ชันหลักคำนวณเวลาการหมัก (Logic Algorithm)
        function calculateBulkMins(t, s, h) {
            // สูตรคำนวณอิงจากอุณหภูมิ 27C เป็นหลัก
            let base = 270 + ((27 - t) * 30);
            return Math.max(Math.round((base * (20 / s)) + (70 - h) * 2), 120);
        }

        // 4. ฟังก์ชันคำนวณและแสดงผลหน้าตารางเวลา (DOM Manipulation)
        function calculateAndGoToTimeline() {
            const startTime = document.getElementById('input-time').value;
            currentConfig.temp = parseFloat(document.getElementById('input-temp').value) || 27;
            currentConfig.starter = parseFloat(document.getElementById('input-starter').value) || 20;
            currentConfig.hydration = parseFloat(document.getElementById('input-hydration').value) || 70;

            // อัปเดตข้อมูลสรุป
            document.getElementById('sum-time').innerText = startTime;
            document.getElementById('sum-temp').innerText = currentConfig.temp + '°C';
            document.getElementById('sum-starter').innerText = currentConfig.starter + '%';

            const totalBulk = calculateBulkMins(currentConfig.temp, currentConfig.starter, currentConfig.hydration);
            const remainingBulk = Math.max(totalBulk - 180, 0); // หักเวลาช่วง Mix และ Fold ออกไป

            // โครงสร้างขั้นตอนการทำขนมปัง
            const steps = [
                { name: 'ผสมแป้ง (Mix)', add: 0, fill: true },
                { name: 'พักแป้ง Autolyse', add: 30, fill: false },
                { name: 'Fold 1', add: 30, fill: false },
                { name: 'พัก 30 นาที', add: 30, fill: false },
                { name: 'Fold 2', add: 30, fill: false },
                { name: 'พัก 30 นาที', add: 30, fill: false },
                { name: 'Fold 3', add: 30, fill: false },
                { name: 'หมักต่อจนจบ Bulk', desc: `รวมใช้เวลา ${Math.floor(totalBulk/60)} ชม. ${totalBulk%60} น.`, add: remainingBulk, fill: false },
                { name: 'ตัดแบ่งและขึ้นรูป (Shape)', add: 0, fill: true },
                { name: 'พักก่อนอบ / Cold Retard', add: 30, fill: false }
            ];

            // สร้าง Element ลงในหน้าเว็บ
            let cumulative = 0;
            document.getElementById('timeline-list').innerHTML = steps.map(s => {
                cumulative += s.add;
                let [h, m] = startTime.split(':').map(Number);
                let d = new Date(); d.setHours(h, m + cumulative);
                
                return `
                    <div class="timeline-item">
                        <div class="timeline-icon ${s.fill ? 'filled' : ''}"><i class="fa-solid fa-circle"></i></div>
                        <div class="timeline-content">
                            <div class="timeline-text">
                                <span class="step-name">${s.name}</span>
                                ${s.desc ? `<span class="step-desc">${s.desc}</span>` : ''}
                            </div>
                            <span class="step-time">${d.toTimeString().slice(0,5)}</span>
                        </div>
                    </div>`;
            }).join('');

            showScreen('timeline', 'right');
        }

        // 5. ฟังก์ชันเตรียมหน้าจอกราฟ
        function drawGraphAndShow() {
            showScreen('graph', 'right');
            // รีเซ็ตปุ่ม Tab ให้กลับมาที่อุณหภูมิปัจจุบัน
            const tabs = document.querySelectorAll('.tab');
            tabs[0].innerText = `${currentConfig.temp}°C (ปัจจุบัน)`;
            updateGraphByTemp(currentConfig.temp, tabs[0]);
        }

        // 6. ฟังก์ชันวาดกราฟลงบน Canvas
        function updateGraphByTemp(temp, el) {
            if(el) {
                document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
                el.classList.add('active');
            }
            
            const bulkMins = calculateBulkMins(temp, currentConfig.starter, currentConfig.hydration);
            const hrs = (bulkMins / 60).toFixed(1);
            
            document.getElementById('graph-bulk-label').innerText = `${hrs} ชั่วโมง`;
            document.getElementById('det-temp').innerText = temp + '°C';
            document.getElementById('det-bulk').innerText = `${Math.floor(bulkMins/60)} ชม. ${bulkMins%60} นาที`;
            document.getElementById('det-starter').innerText = currentConfig.starter + '%';
            document.getElementById('det-hydration').innerText = currentConfig.hydration + '%';

            // เริ่มกระบวนการวาดกราฟ
            const canvas = document.getElementById('bulkChart');
            const ctx = canvas.getContext('2d');
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // วาดแกน
            ctx.strokeStyle = '#D3D0C7'; ctx.lineWidth = 1; ctx.beginPath(); 
            ctx.moveTo(30, 10); ctx.lineTo(30, 180); ctx.lineTo(380, 180); ctx.stroke();

            // คำนวณความชันของเส้นกราฟตามอุณหภูมิ
            let stretchFactor = 0.1 * (27 / temp); 

            // วาดเส้นกราฟ (Sigmoid Curve - แบบจำลองการเจริญเติบโต)
            ctx.strokeStyle = '#2F5A41'; ctx.lineWidth = 3; ctx.beginPath();
            for (let x = 0; x <= 100; x++) {
                let xPos = 30 + (x * 3.2);
                let sigmoid = 1 / (1 + Math.exp(-stretchFactor * (x - 50)));
                let yPos = 180 - (sigmoid * 150);
                x === 0 ? ctx.moveTo(xPos, yPos) : ctx.lineTo(xPos, yPos);
            }
            ctx.stroke();

            // วาดเส้นประแสดงเป้าหมาย (จุดที่ขยายตัว 30-40%)
            ctx.setLineDash([5, 5]); ctx.strokeStyle = '#d35400'; ctx.beginPath();
            ctx.moveTo(30 + (80 * 3.2), 10); ctx.lineTo(30 + (80 * 3.2), 180); ctx.stroke();
            ctx.setLineDash([]);
        }
    </script>
</body>
</html>
