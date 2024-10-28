<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบรายงานงบประมาณ</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            background-color: #f8fafc;
            font-family: 'Sarabun', sans-serif;
        }
        .header {
            background-color: #fff;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .card {
            border-radius: 10px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            margin-bottom: 20px;
            border: none;
        }
        .summary-card {
            transition: transform 0.2s;
        }
        .summary-card:hover {
            transform: translateY(-5px);
        }
        .btn-group {
            gap: 10px;
        }
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1000;
        }
        .modal.show {
            display: block;
        }
        .modal-content {
            background: white;
            border-radius: 10px;
            padding: 20px;
            margin: 50px auto;
            max-width: 800px;
            width: 90%;
        }
        .bg-purple {
            background-color: #6b46c1;
        }
        .filter-section {
            background: white;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        .chart-container {
            background: white;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        .table th {
            background-color: #f8fafc;
        }
        .badge {
            padding: 8px 12px;
            font-weight: normal;
        }
    </style>
</head>
<body>
    <!-- Header with Logos -->
    <div class="header mb-4">
        <div class="container">
            <div class="d-flex justify-content-between align-items-center py-3">
                <div class="d-flex align-items-center">
                    <img src="logo-tcr.png" alt="TCR Logo" height="80" class="me-3">
                    <img src="logo-rayong.png" alt="Rayong Logo" height="80" class="me-3">
                </div>
                <h2 class="text-center mb-0">ระบบรายงานงบประมาณ</h2>
                <div class="text-end">
                    <div class="small text-muted">ข้อมูล ณ วันที่ <span id="currentDate"></span></div>
                    <div class="small text-muted">ผู้ใช้งาน: <span id="currentUserName"></span></div>
                </div>
            </div>
        </div>
    </div>

    <div class="container">
        <!-- Filter Section -->
        <div class="filter-section">
            <div class="row g-3">
                <div class="col-md-2">
                    <select class="form-select" id="filterYear" onchange="filterAndSearchData()">
                        <option value="">ทุกปีงบประมาณ</option>
                    </select>
                </div>
                <div class="col-md-2">
                    <select class="form-select" id="filterSource" onchange="filterAndSearchData()">
                        <option value="">ทุกแหล่งงบประมาณ</option>
                    </select>
                </div>
                <div class="col-md-2">
                    <select class="form-select" id="filterStatus" onchange="filterAndSearchData()">
                        <option value="">ทุกสถานะ</option>
                    </select>
                </div>
                <div class="col-md-4">
                    <div class="input-group">
                        <input type="text" class="form-control" id="searchInput" 
                               placeholder="ค้นหาโครงการ..." onkeyup="filterAndSearchData()">
                        <button class="btn btn-outline-secondary" type="button">
                            <i class="fas fa-search"></i>
                        </button>
                    </div>
                </div>
                <div class="col-md-2">
                    <button class="btn btn-outline-secondary w-100" onclick="resetFilters()">
                        <i class="fas fa-redo me-2"></i>รีเซ็ต
                    </button>
                </div>
            </div>
        </div>

        <!-- Summary Cards -->
        <div class="row mb-4">
            <div class="col-md-2">
                <div class="card summary-card bg-primary text-white">
                    <div class="card-body">
                        <h6 class="card-subtitle mb-2">งบประมาณรวม</h6>
                        <h3 class="card-title mb-0" id="totalBudget">฿0.00</h3>
                    </div>
                </div>
            </div>
            <div class="col-md-2">
                <div class="card summary-card bg-success text-white">
                    <div class="card-body">
                        <h6 class="card-subtitle mb-2">เบิกจ่ายแล้ว</h6>
                        <h3 class="card-title mb-0" id="totalSpent">฿0.00</h3>
                    </div>
                </div>
            </div>
            <div class="col-md-2">
                <div class="card summary-card bg-info text-white">
                    <div class="card-body">
                        <h6 class="card-subtitle mb-2">คงเหลือ</h6>
                        <h3 class="card-title mb-0" id="totalBalance">฿0.00</h3>
                    </div>
                </div>
            </div>
            <div class="col-md-2">
                <div class="card summary-card bg-warning text-white">
                    <div class="card-body">
                        <h6 class="card-subtitle mb-2">โอนคืน อบจ.</h6>
                        <h3 class="card-title mb-0" id="totalReturned">฿0.00</h3>
                    </div>
                </div>
            </div>
            <div class="col-md-2">
                <div class="card summary-card bg-purple text-white">
                    <div class="card-body">
                        <h6 class="card-subtitle mb-2">รายได้สะสม</h6>
                        <h3 class="card-title mb-0" id="totalAccumulated">฿0.00</h3>
                    </div>
                </div>
            </div>
            <div class="col-md-2">
                <div class="card summary-card bg-secondary text-white">
                    <div class="card-body">
                        <h6 class="card-subtitle mb-2">จำนวนโครงการ</h6>
                        <h3 class="card-title mb-0" id="projectCount">0</h3>
                    </div>
                </div>
            </div>
        </div>
<!-- Action Buttons -->
        <div class="mb-4">
            <button class="btn btn-primary" onclick="showForm()">
                <i class="fas fa-plus me-2"></i>เพิ่มข้อมูล
            </button>
            <button class="btn btn-outline-primary" onclick="showImportModal()">
                <i class="fas fa-file-import me-2"></i>นำเข้าข้อมูล
            </button>
            <button class="btn btn-outline-success" onclick="exportToExcel()">
                <i class="fas fa-file-export me-2"></i>ส่งออกข้อมูล
            </button>
        </div>

        <!-- Charts Section -->
        <div class="row mb-4">
            <div class="col-md-6">
                <div class="chart-container">
                    <h5>สัดส่วนงบประมาณตามแหล่งที่มา</h5>
                    <canvas id="sourcePieChart"></canvas>
                </div>
            </div>
            <div class="col-md-6">
                <div class="chart-container">
                    <h5>แนวโน้มการใช้งบประมาณ</h5>
                    <canvas id="spendingLineChart"></canvas>
                </div>
            </div>
        </div>

        <!-- Data Table -->
        <div class="card">
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table table-hover">
                        <thead>
                            <tr>
                                <th>ปีงบประมาณ</th>
                                <th>โครงการ</th>
                                <th>ที่มา</th>
                                <th class="text-end">งบประมาณ</th>
                                <th class="text-end">เบิกจ่าย</th>
                                <th class="text-end">โอนคืน อบจ.</th>
                                <th class="text-end">รายได้สะสม</th>
                                <th class="text-end">คงเหลือ</th>
                                <th class="text-center">สถานะ</th>
                                <th class="text-center">จัดการ</th>
                            </tr>
                        </thead>
                        <tbody id="budgetTableBody"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <!-- Add/Edit Modal -->
    <div id="formModal" class="modal">
        <div class="modal-content">
            <h3 class="mb-4">เพิ่ม/แก้ไขข้อมูล</h3>
            <form id="budgetForm" onsubmit="handleSubmit(event)">
                <input type="hidden" id="editItemId">
                <div class="row g-3">
                    <div class="col-md-6">
                        <label class="form-label">ปีงบประมาณ</label>
                        <input type="text" class="form-control" id="year" required>
                    </div>
                    <div class="col-12">
                        <label class="form-label">ชื่อโครงการ</label>
                        <input type="text" class="form-control" id="projectName" required>
                    </div>
                    <div class="col-12">
                        <label class="form-label">ที่มาของงบประมาณ</label>
                        <select class="form-select" id="budgetSource" required>
                            <option value="">เลือกที่มาของงบประมาณ</option>
                            <option value="เงินรายได้">เงินรายได้</option>
                            <option value="เงินอุดหนุน">เงินอุดหนุน</option>
                            <option value="เงินสะสม">เงินสะสม</option>
                            <option value="อื่นๆ">อื่นๆ</option>
                        </select>
                    </div>
                    <div class="col-md-4">
                        <label class="form-label">งบประมาณ</label>
                        <input type="number" class="form-control" id="budget" step="0.01" min="0" required>
                    </div>
                    <div class="col-md-4">
                        <label class="form-label">เบิกจ่าย</label>
                        <input type="number" class="form-control" id="spent" step="0.01" min="0">
                    </div>
                    <div class="col-md-4">
                        <label class="form-label">โอนคืน อบจ.ระยอง</label>
                        <input type="number" class="form-control" id="returnedToRayong" step="0.01" min="0">
                    </div>
                    <div class="col-md-4">
                        <label class="form-label">ตกเป็นรายได้สะสม</label>
                        <input type="number" class="form-control" id="accumulatedIncome" step="0.01" min="0">
                    </div>
                    <div class="col-md-12">
                        <label class="form-label">สถานะ</label>
                        <select class="form-select" id="status">
                            <option value="กำลังดำเนินการ">กำลังดำเนินการ</option>
                            <option value="เสร็จสิ้น">เสร็จสิ้น</option>
                            <option value="ยกเลิก">ยกเลิก</option>
                        </select>
                    </div>
                </div>
                <div class="mt-4 text-end">
                    <button type="button" class="btn btn-secondary" onclick="hideForm()">ยกเลิก</button>
                    <button type="submit" class="btn btn-primary" id="submitBtn">บันทึก</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Import Modal -->
    <div id="importModal" class="modal">
        <div class="modal-content">
            <h3 class="mb-4">นำเข้าข้อมูล</h3>
            <div class="border rounded p-4 text-center">
                <i class="fas fa-file-upload fa-3x mb-3"></i>
                <p>คลิกเพื่อเลือกไฟล์ หรือลากไฟล์มาวาง</p>
                <input type="file" id="fileInput" accept=".csv,.xlsx" style="display: none">
                <button class="btn btn-primary" onclick="document.getElementById('fileInput').click()">
                    เลือกไฟล์
                </button>
            </div>
            <div class="mt-4 text-end">
                <button class="btn btn-secondary" onclick="hideImportModal()">ปิด</button>
            </div>
        </div>
    </div>

    <!-- Scripts -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>
<script>
        let budgetData = [];
        let sourcePieChart, spendingLineChart;

        // โหลดข้อมูลเมื่อเปิดหน้าเว็บ
        document.addEventListener('DOMContentLoaded', function() {
            const savedData = localStorage.getItem('budgetData');
            if (savedData) {
                budgetData = JSON.parse(savedData);
            }
            initializeCharts();
            updateFilterOptions();
            renderTable(budgetData);
            updateSummary();
            updateCharts(budgetData);
            document.getElementById('currentDate').textContent = new Date().toLocaleDateString('th-TH');
            document.getElementById('currentUserName').textContent = 'ผู้ดูแลระบบ';
        });

        // ฟังก์ชันกรองและค้นหา
        function filterAndSearchData() {
            const yearFilter = document.getElementById('filterYear').value;
            const sourceFilter = document.getElementById('filterSource').value;
            const statusFilter = document.getElementById('filterStatus').value;
            const searchText = document.getElementById('searchInput').value.toLowerCase();

            const filteredData = budgetData.filter(item => {
                const matchYear = !yearFilter || item.year === yearFilter;
                const matchSource = !sourceFilter || item.budgetSource === sourceFilter;
                const matchStatus = !statusFilter || item.status === statusFilter;
                const matchSearch = !searchText || 
                    item.projectName.toLowerCase().includes(searchText) ||
                    item.year.includes(searchText);

                return matchYear && matchSource && matchStatus && matchSearch;
            });

            renderTable(filteredData);
            updateCharts(filteredData);
        }

        function resetFilters() {
            document.getElementById('filterYear').value = '';
            document.getElementById('filterSource').value = '';
            document.getElementById('filterStatus').value = '';
            document.getElementById('searchInput').value = '';
            filterAndSearchData();
        }

        function updateFilterOptions() {
            const years = [...new Set(budgetData.map(item => item.year))];
            const sources = [...new Set(budgetData.map(item => item.budgetSource))];
            const statuses = [...new Set(budgetData.map(item => item.status))];

            const yearSelect = document.getElementById('filterYear');
            const sourceSelect = document.getElementById('filterSource');
            const statusSelect = document.getElementById('filterStatus');

            yearSelect.innerHTML = '<option value="">ทุกปีงบประมาณ</option>' +
                years.map(year => `<option value="${year}">${year}</option>`).join('');
            
            sourceSelect.innerHTML = '<option value="">ทุกแหล่งงบประมาณ</option>' +
                sources.map(source => `<option value="${source}">${source}</option>`).join('');
            
            statusSelect.innerHTML = '<option value="">ทุกสถานะ</option>' +
                statuses.map(status => `<option value="${status}">${status}</option>`).join('');
        }

        // Modal Controls
        function showForm() {
            document.getElementById('formModal').classList.add('show');
        }

        function hideForm() {
            document.getElementById('formModal').classList.remove('show');
            document.getElementById('budgetForm').reset();
            document.getElementById('editItemId').value = '';
            document.getElementById('submitBtn').textContent = 'บันทึก';
        }

        function showImportModal() {
            document.getElementById('importModal').classList.add('show');
        }

        function hideImportModal() {
            document.getElementById('importModal').classList.remove('show');
        }

        // Form Submit Handler
        function handleSubmit(event) {
            event.preventDefault();
            
            const editId = document.getElementById('editItemId').value;
            const formData = {
                year: document.getElementById('year').value,
                projectName: document.getElementById('projectName').value,
                budgetSource: document.getElementById('budgetSource').value,
                budget: parseFloat(document.getElementById('budget').value || 0).toFixed(2),
                spent: parseFloat(document.getElementById('spent').value || 0).toFixed(2),
                returnedToRayong: parseFloat(document.getElementById('returnedToRayong').value || 0).toFixed(2),
                accumulatedIncome: parseFloat(document.getElementById('accumulatedIncome').value || 0).toFixed(2),
                status: document.getElementById('status').value
            };

            formData.balance = (
                parseFloat(formData.budget) - 
                parseFloat(formData.spent) - 
                parseFloat(formData.returnedToRayong) -
                parseFloat(formData.accumulatedIncome)
            ).toFixed(2);

            if (editId) {
                const index = budgetData.findIndex(item => item.id === parseInt(editId));
                if (index !== -1) {
                    budgetData[index] = { ...budgetData[index], ...formData };
                }
            } else {
                budgetData.push({ id: Date.now(), ...formData });
            }

            localStorage.setItem('budgetData', JSON.stringify(budgetData));
            updateFilterOptions();
            renderTable(budgetData);
            updateSummary();
            updateCharts(budgetData);
            hideForm();
        }

        // Table Rendering
        function renderTable(data) {
            const tbody = document.getElementById('budgetTableBody');
            tbody.innerHTML = '';
            
            data.forEach(item => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${item.year}</td>
                    <td>${item.projectName}</td>
                    <td>${item.budgetSource}</td>
                    <td class="text-end">฿${formatNumber(item.budget)}</td>
                    <td class="text-end">฿${formatNumber(item.spent)}</td>
                    <td class="text-end">฿${formatNumber(item.returnedToRayong)}</td>
                    <td class="text-end">฿${formatNumber(item.accumulatedIncome)}</td>
                    <td class="text-end">฿${formatNumber(item.balance)}</td>
                    <td class="text-center">
                        <span class="badge bg-${getStatusColor(item.status)}">${item.status}</span>
                    </td>
                    <td class="text-center">
                        <button class="btn btn-sm btn-outline-primary me-1" onclick="editItem(${item.id})">
                            <i class="fas fa-edit"></i>
                        </button>
                        <button class="btn btn-sm btn-outline-danger" onclick="deleteItem(${item.id})">
                            <i class="fas fa-trash"></i>
                        </button>
                    </td>
                `;
                tbody.appendChild(row);
            });
        }

        // Charts
        function initializeCharts() {
            const ctx1 = document.getElementById('sourcePieChart').getContext('2d');
            const ctx2 = document.getElementById('spendingLineChart').getContext('2d');

            sourcePieChart = new Chart(ctx1, {
                type: 'pie',
                data: {
                    labels: [],
                    datasets: [{
                        data: [],
                        backgroundColor: ['#FF6384', '#36A2EB', '#FFCE56', '#4BC0C0']
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        title: {
                            display: true,
                            text: 'สัดส่วนงบประมาณตามแหล่งที่มา'
                        }
                    }
                }
            });

            spendingLineChart = new Chart(ctx2, {
                type: 'line',
                data: {
                    labels: [],
                    datasets: [{
                        label: 'งบประมาณ',
                        data: [],
                        borderColor: '#36A2EB'
                    }, {
                        label: 'เบิกจ่าย',
                        data: [],
                        borderColor: '#FF6384'
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        title: {
                            display: true,
                            text: 'แนวโน้มการใช้งบประมาณ'
                        }
                    }
                }
            });
        }

        function updateCharts(data) {
            // อัพเดทข้อมูลกราฟวงกลม
            const sourceData = {};
            data.forEach(item => {
                sourceData[item.budgetSource] = (sourceData[item.budgetSource] || 0) + parseFloat(item.budget);
            });

            sourcePieChart.data.labels = Object.keys(sourceData);
            sourcePieChart.data.datasets[0].data = Object.values(sourceData);
            sourcePieChart.update();

            // อัพเดทข้อมูลกราฟเส้น
            const years = [...new Set(data.map(item => item.year))].sort();
            const budgetByYear = years.map(year => {
                return data
                    .filter(item => item.year === year)
                    .reduce((sum, item) => sum + parseFloat(item.budget), 0);
            });
            const spentByYear = years.map(year => {
                return data
                    .filter(item => item.year === year)
                    .reduce((sum, item) => sum + parseFloat(item.spent), 0);
            });

            spendingLineChart.data.labels = years;
            spendingLineChart.data.datasets[0].data = budgetByYear;
            spendingLineChart.data.datasets[1].data = spentByYear;
            spendingLineChart.update();
        }

        // Utility Functions
        function formatNumber(num) {
            return parseFloat(num).toLocaleString('th-TH', {
                minimumFractionDigits: 2,
                maximumFractionDigits: 2
            });
        }

        function getStatusColor(status) {
            switch(status) {
                case 'เสร็จสิ้น': return 'success';
                case 'กำลังดำเนินการ': return 'primary';
                case 'ยกเลิก': return 'danger';
                default: return 'secondary';
            }
        }

        function updateSummary() {
            const summary = budgetData.reduce((acc, item) => ({
                totalBudget: acc.totalBudget + parseFloat(item.budget),
                totalSpent: acc.totalSpent + parseFloat(item.spent),
                totalReturned: acc.totalReturned + parseFloat(item.returnedToRayong),
                totalAccumulated: acc.totalAccumulated + parseFloat(item.accumulatedIncome),
                totalBalance: acc.totalBalance + parseFloat(item.balance)
            }), { 
                totalBudget: 0, 
                totalSpent: 0, 
                totalReturned: 0,
                totalAccumulated: 0,
                totalBalance: 0 
            });

            document.getElementById('totalBudget').textContent = `฿${formatNumber(summary.totalBudget)}`;
            document.getElementById('totalSpent').textContent = `฿${formatNumber(summary.totalSpent)}`;
            document.getElementById('totalReturned').textContent = `฿${formatNumber(summary.totalReturned)}`;
            document.getElementById('totalAccumulated').textContent = `฿${formatNumber(summary.totalAccumulated)}`;
            document.getElementById('totalBalance').textContent = `฿${formatNumber(summary.totalBalance)}`;
            document.getElementById('projectCount').textContent = budgetData.length;
        }

        // Export to Excel
        function exportToExcel() {
            const ws = XLSX.utils.json_to_sheet(budgetData.map(item => ({
                'ปีงบประมาณ': item.year,
                'โครงการ': item.projectName,
                'ที่มาของงบประมาณ': item.budgetSource,
                'งบประมาณ': parseFloat(item.budget),
                'เบิกจ่าย': parseFloat(item.spent),
                'โอนคืน อบจ.ระยอง': parseFloat(item.returnedToRayong),
                'ตกเป็นรายได้สะสม': parseFloat(item.accumulatedIncome),
                'คงเหลือ': parseFloat(item.balance),
                'สถานะ': item.status
            })));

            const wb = XLSX.utils.book_new();
            wb.Props = {
                Title: "รายงานงบประมาณ",
                Author: "ระบบรายงานงบประมาณ",
                CreatedDate: new Date()
            };

            XLSX.utils.book_append_sheet(wb, ws, "รายงานงบประมาณ");
            
            const fileName = `รายงานงบประมาณ_${new Date().toLocaleDateString('th-TH')}.xlsx`;
            XLSX.writeFile(wb, fileName);
        }
    </script>
</body>
</html>
