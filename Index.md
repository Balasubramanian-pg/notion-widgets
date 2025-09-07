<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Notion Calendar Widget</title>
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Outfit', sans-serif;
        }
        
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: #f5f5f7;
            padding: 20px;
        }
        
        .calendar-widget {
            width: 100%;
            max-width: 360px;
            background: white;
            border-radius: 18px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.08);
            overflow: hidden;
            padding: 24px;
            border: 1px solid #e0e0e0;
        }
        
        .calendar-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 22px;
        }
        
        .nav-btn {
            width: 36px;
            height: 36px;
            border: none;
            border-radius: 10px;
            background: #f8f9fa;
            color: #2d3436;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 16px;
            font-weight: 600;
            transition: all 0.2s ease;
        }
        
        .nav-btn:hover {
            background: #e9ecef;
            transform: scale(1.05);
        }
        
        .nav-btn:active {
            transform: scale(0.95);
        }
        
        .calendar-header h1 {
            font-weight: 600;
            font-size: 20px;
            color: #2d3436;
            letter-spacing: 0.5px;
            flex: 1;
            text-align: center;
        }
        
        .weekdays {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 8px;
            margin-bottom: 12px;
            text-align: center;
        }
        
        .weekdays div {
            font-weight: 500;
            font-size: 13px;
            color: #636e72;
            padding: 8px 0;
        }
        
        .days {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 6px;
        }
        
        .days div {
            height: 38px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 10px;
            font-weight: 500;
            color: #2d3436;
            cursor: pointer;
            transition: all 0.2s ease;
            font-size: 15px;
            position: relative;
        }
        
        .days div:hover:not(.different-month) {
            background: #f8f9fa;
            transform: scale(1.05);
        }
        
        .different-month {
            color: #b2bec3;
        }
        
        .today {
            background: #4e54c8;
            color: white;
            font-weight: 600;
            box-shadow: 0 4px 10px rgba(78, 84, 200, 0.25);
        }
        
        .today:hover {
            background: #3f44ad !important;
        }
        
        .event-day::after {
            content: "";
            position: absolute;
            bottom: 4px;
            width: 4px;
            height: 4px;
            background: #4e54c8;
            border-radius: 50%;
        }
        
        .today.event-day::after {
            background: white;
        }
        
        .calendar-footer {
            margin-top: 20px;
            text-align: center;
            padding-top: 15px;
            border-top: 1px solid #f1f2f6;
            color: #636e72;
            font-size: 13px;
        }
        
        @media (max-width: 400px) {
            .calendar-widget {
                transform: scale(0.9);
            }
        }
    </style>
</head>
<body>
    <div class="calendar-widget">
        <div class="calendar-header">
            <button class="nav-btn" id="prevBtn">‹</button>
            <h1 id="monthYear">September 2025</h1>
            <button class="nav-btn" id="nextBtn">›</button>
        </div>
        
        <div class="weekdays">
            <div>M</div>
            <div>T</div>
            <div>W</div>
            <div>T</div>
            <div>F</div>
            <div>S</div>
            <div>S</div>
        </div>
        
        <div class="days" id="daysContainer">
        </div>
        
        <div class="calendar-footer">
            Born To Make A Difference
        </div>
    </div>

    <script>
        class Calendar {
            constructor() {
                this.currentDate = new Date();
                this.today = new Date();
                this.monthNames = [
                    'January', 'February', 'March', 'April', 'May', 'June',
                    'July', 'August', 'September', 'October', 'November', 'December'
                ];
                
                this.init();
            }
            
            init() {
                this.render();
                this.bindEvents();
            }
            
            bindEvents() {
                document.getElementById('prevBtn').addEventListener('click', () => {
                    this.currentDate.setMonth(this.currentDate.getMonth() - 1);
                    this.render();
                });
                
                document.getElementById('nextBtn').addEventListener('click', () => {
                    this.currentDate.setMonth(this.currentDate.getMonth() + 1);
                    this.render();
                });
            }
            
            render() {
                this.renderHeader();
                this.renderDays();
            }
            
            renderHeader() {
                const monthYear = document.getElementById('monthYear');
                monthYear.textContent = `${this.monthNames[this.currentDate.getMonth()]} ${this.currentDate.getFullYear()}`;
            }
            
            renderDays() {
                const daysContainer = document.getElementById('daysContainer');
                daysContainer.innerHTML = '';
                
                const year = this.currentDate.getFullYear();
                const month = this.currentDate.getMonth();
                
                // Get first day of month and adjust for Monday start
                const firstDay = new Date(year, month, 1);
                let startDate = new Date(firstDay);
                const dayOfWeek = (firstDay.getDay() + 6) % 7; // Convert Sunday=0 to Monday=0
                startDate.setDate(firstDay.getDate() - dayOfWeek);
                
                // Generate 42 days (6 weeks)
                for (let i = 0; i < 42; i++) {
                    const date = new Date(startDate);
                    date.setDate(startDate.getDate() + i);
                    
                    const dayElement = document.createElement('div');
                    dayElement.textContent = date.getDate();
                    
                    // Check if it's from different month
                    if (date.getMonth() !== month) {
                        dayElement.classList.add('different-month');
                    }
                    
                    // Check if it's today
                    if (date.toDateString() === this.today.toDateString()) {
                        dayElement.classList.add('today');
                    }
                    
                    // Add random events (but keep consistent per day)
                    const dateKey = `${date.getFullYear()}-${date.getMonth()}-${date.getDate()}`;
                    if (this.hasEvent(dateKey) && date.getMonth() === month) {
                        dayElement.classList.add('event-day');
                    }
                    
                    // Add click event
                    dayElement.addEventListener('click', () => {
                        if (!dayElement.classList.contains('different-month')) {
                            const monthName = this.monthNames[month];
                            alert(`You selected ${date.getDate()} ${monthName} ${year}`);
                        }
                    });
                    
                    daysContainer.appendChild(dayElement);
                }
            }
            
            // Simple hash function to determine if a day has events
            hasEvent(dateKey) {
                let hash = 0;
                for (let i = 0; i < dateKey.length; i++) {
                    const char = dateKey.charCodeAt(i);
                    hash = ((hash << 5) - hash) + char;
                    hash = hash & hash; // Convert to 32-bit integer
                }
                return Math.abs(hash) % 10 < 3; // 30% chance of having an event
            }
        }
        
        document.addEventListener('DOMContentLoaded', function() {
            new Calendar();
        });
    </script>
</body>
</html>
