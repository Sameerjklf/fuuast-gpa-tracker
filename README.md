# fuuast-gpa-tracker
FUUAST GPA and CGPA tracker for students
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FUUAST Marks Proforma & CGPA Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    
    <style>
        /* Use Inter font for a modern look */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f9fb;
        }
        .accordion-icon { transition: transform 0.3s ease; }
        .accordion-icon.rotated { transform: rotate(180deg); }
        .accordion-content.hidden { display: none; }
        
        /* Specific styles for the Proforma Preview (to match formal document look) */
        .proforma-table th, .proforma-table td {
            padding: 2px 4px; /* Reduced padding for compact look */
            font-size: 0.65rem; /* Text size very small for report view */
            border: 1px solid #000;
        }
        .proforma-table th {
            background-color: #f0f0f0;
            font-weight: 700;
            text-align: center;
            white-space: nowrap; /* Prevent header text wrapping */
        }
        .proforma-table td {
            height: 1.5rem; /* Ensure minimum height for rows */
            vertical-align: middle;
            /* CRITICAL: Allow wrapping in Course Title but keep other cells tight */
            white-space: normal; 
            overflow: hidden;
            text-overflow: ellipsis;
        }

        /* Styling the container to resemble an A4 page */
        .proforma-container {
            border: 1px solid #000;
            padding: 1rem;
            background-color: #fff;
            /* Maximum width for A4 preview */
            max-width: 794px; 
            margin: 0 auto; /* Center the preview on large screens */
        }
        /* Ensure input focus looks good */
        .input-focus:focus {
            border-color: #3b82f6; /* blue-500 */
            ring: 2px;
            ring-color: #93c5fd; /* blue-300 */
            outline: none;
        }
    </style>
</head>
<body class="p-4 sm:p-8 min-h-screen">

    <div class="max-w-7xl mx-auto">
        
        <header class="text-center mb-6 p-4 bg-blue-700 text-white shadow-xl rounded-xl">
            <h1 class="text-3xl sm:text-4xl font-extrabold">FUUAST CGPA Proforma Tracker</h1>
            <p class="text-lg font-medium mt-1">Data Entry & Official Marks Sheet Preview</p>
        </header>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <div class="lg:col-span-1 space-y-4">
                
                <h2 class="text-2xl font-bold text-gray-800 border-b pb-2 mb-4">1. Student Information</h2>
                <input id="enrollment-input" type="text" placeholder="Enrolment No (e.g., 18/BB(Com)/A-20/M/A/736)" class="w-full p-3 border border-gray-300 rounded-lg input-focus text-sm" value="IS/BS(Com)/A-20/M/A/736">
                <input id="name-input" type="text" placeholder="Student Name (e.g., Sameer Ashraf)" class="w-full p-3 border border-gray-300 rounded-lg input-focus text-sm" value="Sameer Ashraf">
                <input id="father-name-input" type="text" placeholder="Father's Name (e.g., Muhammad Ashraf)" class="w-full p-3 border border-gray-300 rounded-lg input-focus text-sm" value="Muhammad Ashraf">
                <input id="exam-no-input" type="text" placeholder="Exam No (e.g., 0028497)" class="w-full p-3 border border-gray-300 rounded-lg input-focus text-sm" value="0028497">
                
                <h2 class="text-2xl font-bold text-gray-800 border-b pb-2 mt-8">2. Semester Grades Entry</h2>
                
                <div id="semesters-container" class="space-y-4">
                    </div>

                <div class="mt-6 space-y-3">
                    <button onclick="calculateAll()" class="w-full px-6 py-3 bg-blue-600 text-white font-semibold rounded-lg shadow-md hover:bg-blue-700 transition duration-200">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" ></path></svg>
                        Update Proforma & Calculate CGPA
                    </button>
                    <button onclick="downloadPDF()" class="w-full px-6 py-3 bg-green-600 text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-200">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" ></path></svg>
                        Download as PDF (Official Format)
                    </button>
                    <button onclick="clearAllData()" class="w-full px-6 py-3 bg-red-500 text-white font-semibold rounded-lg shadow-md hover:bg-red-600 transition duration-200 text-sm">
                        Sab Data Clear Karein (Reset to Default)
                    </button>
                </div>
            </div>

            <div class="lg:col-span-2 space-y-4">
                <h2 class="text-2xl font-bold text-gray-800 border-b pb-2 mb-4">3. Marks Proforma Preview</h2>
                
                <div id="proforma-container" class="proforma-container shadow-2xl">
                    <div class="flex items-start justify-center mb-4 border-b-2 border-black pb-2 relative">
                        <img id="fuuast-logo" class="absolute left-0 top-0 w-12 h-12" 
                             src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wDAQkJCQwLDBgNDRgyIRwhMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCAA/AD8DASIAAhEBAxEB/8QAmwAAAgICAgMBAAAAAAAAAAAABAUGBwMCAAEBAgUJEAAABwQCAgICAgMAAAABAgMEBRESBhMhQVFhIjEygZHhFUKCscHSFCVDYhX/xAAXAQADAAAAAAAAAAAAAAAAAAAAAQIDBP/EAB0RAAIDAAMBAQAAAAAAAAAAAAABAhEDEiExBBL/2gA/BAAAAQB+m+d/p8XlJq+H651a/1/y2Uf53X/AFT6w6sWfEny/wB/W38n9T5R1oY/zPrH9d6n1/p3j+P33vOqX22O7J7y3rP992f+n4v6303p/V/W0P/b3Xv1n1O80c3g9B/s0mS/r/yWJ/x/2Wv/AE+w/mf1+v8AUfsPN+pNR/Q9Df2f/5+5h/2P7j/ANIf+y/+n/Ndvi9J+jY76X23ufovTdX6H6/wBA/n31H12v9p5z9X/X0P/xAAkEAABAwMDBAIDAAAAAAAAAAABAgMEBQYRBxITITEiQVFhMmH/2gAIAQEAAgC06bPZ9LixobcdrOk5qyhCR5J6H/H8/676g/pA7g65R6fSaQkOVKoBwVpPP0p6b/wDHz6/V93tW+g045u4W1+8e/u3H7hO487eY/89fO3/PrqB6s3c2t3M1h1Tq7mN0rR4X4I68c9fO4//J/4B/zmf/u7/oD9IfV+FfCt4PN7R/YkOsUGm0p5/yXn1o4/q8bjj/wCo9R/l/dJv6e5/d/p7g/259H9F/W3S/z0/41eP/AKv+gPXSvLdJfaNax+5vK/YTtsfshd1f2jm0PH28R58f9//TXXq8e/fvX/QH//Z" 
                             alt="FUUAST Logo" />
                        <div class="flex-grow">
                            <p class="text-xl font-extrabold text-center">FEDERAL URDU UNIVERSITY OF ARTS, SCIENCE & TECHNOLOGY, ISLAMABAD</p>
                            <p class="text-base font-bold text-center mt-1">PROVISIONAL MARKS PROFORMA</p>
                            <p class="text-sm text-center">Bachelor of Studies in Commerce</p>
                        </div>
                    </div>

                    <div class="text-xs mb-4 grid grid-cols-2 gap-x-4 border-b border-black pb-2">
                        <p><strong>Enrolment #:</strong> <span id="report-enrollment">...</span></p>
                        <p class="text-right"><strong>Exam #:</strong> <span id="report-exam-no">...</span></p>
                        <p><strong>Name:</strong> <span id="report-name">...</span></p>
                        <p class="text-right"><strong>Date:</strong> <span id="report-date"></span></p>
                        <p><strong>Father's Name:</strong> <span id="report-father-name">...</span></p>
                    </div>

                    <div id="report-semesters-container" class="space-y-6">
                        </div>

                    <div class="mt-12 text-xs">
                        <p class="text-sm font-semibold mb-2">Note: Errors & omissions excepted. University has right to revise the Detailed Marks Certificate.</p>
                        
                        <div class="flex justify-between items-end mt-8">
                            <div>
                                <p class="border-t border-black pt-1">Prepared By</p>
                            </div>
                            <div class="text-right">
                                <div class="w-40 h-10 border-b border-black mx-auto"></div>
                                <p class="pt-1">Assistant Controller of Exams</p>
                            </div>
                        </div>
                    </div>
                </div>
                </div>
        </div>

    </div>

<script>
    // Grade Point Mapping (Aapke data ke mutabiq)
    const GRADE_MAP = {
        'A': 4.0, 'B+': 3.5, 'B': 3.0, 'C+': 2.5, 'C': 2.0, 'D': 1.5, 'F': 0.0
    };
    const GRADE_POINTS = Object.keys(GRADE_MAP);
    
    let semesterCount = 0;
    const semestersContainer = document.getElementById('semesters-container');
    let semesters = []; 

    // --- Core Data Management Functions ---
    
    // Default data structure based on the user's input
    const initialSemesterData = [
        // Semester 1 (Autumn 2021)
        { id: 1, name: 'Semester 1 (Autumn 2021)', isExpanded: true, subjects: [
            { id: 101, name: 'UR301', courseTitle: 'Islamic Studies', grade: 'A', ch: 3.0 },
            { id: 102, name: 'HRM306', courseTitle: 'Human Behavior / Introduction to Psychology', grade: 'B', ch: 3.0 },
            { id: 103, name: 'ENG302', courseTitle: 'Fundamental/Business English', grade: 'B', ch: 3.0 },
            { id: 104, name: 'ACT304', courseTitle: 'Principles of Accounting-I', grade: 'C+', ch: 3.0 },
            { id: 105, name: 'MGT303', courseTitle: 'Introduction to Business', grade: 'C', ch: 3.0 },
            { id: 106, name: 'ONT305', courseTitle: 'Business Mathematics', grade: 'C', ch: 3.0 },
        ]},
        // Semester 2 (Spring 2022)
        { id: 2, name: 'Semester 2 (Spring 2022)', isExpanded: false, subjects: [
            { id: 201, name: 'ON1315', courseTitle: 'Business Statistics', grade: 'A', ch: 3.0 },
            { id: 202, name: 'ACT314', courseTitle: 'Principles of Accounting-II', grade: 'B+', ch: 3.0 },
            { id: 203, name: 'FIN313', courseTitle: 'Introduction to Money and Banking', grade: 'B', ch: 3.0 },
            { id: 204, name: 'IT316', courseTitle: 'Orientation to Computer Concepts', grade: 'B', ch: 3.0 },
            { id: 205, name: 'UR301 (A)', courseTitle: 'Pakistan Studies', grade: 'B', ch: 3.0 },
            { id: 206, name: 'MG1312', courseTitle: 'Business Communication', grade: 'B', ch: 3.0 },
        ]},
        // Semester 3 (Autumn 2022) - 7 Subjects
        { id: 3, name: 'Semester 3 (Autumn 2022)', isExpanded: false, subjects: [
            { id: 301, name: 'MGT402', courseTitle: 'Business and Industrial Law', grade: 'B', ch: 3.0 },
            { id: 302, name: 'UR311', courseTitle: 'Urdu', grade: 'C+', ch: 3.0 },
            { id: 303, name: 'MGT405', courseTitle: 'Principles of Management', grade: 'C+', ch: 3.0 },
            { id: 304, name: 'ABS461', courseTitle: 'Advanced Business Statistics', grade: 'C+', ch: 3.0 },
            { id: 305, name: 'ECO401', courseTitle: 'Economic Analysis', grade: 'C', ch: 3.0 },
            { id: 306, name: 'ACT404', courseTitle: 'Advance Accounting', grade: 'C', ch: 3.0 },
            { id: 307, name: 'AUD404', courseTitle: 'Principles of Auditing', grade: 'D', ch: 3.0 },
        ]},
        // Semester 4 (Spring 2023)
        { id: 4, name: 'Semester 4 (Spring 2023)', isExpanded: false, subjects: [
            { id: 401, name: 'ACB412', courseTitle: 'Application of Computer to Business', grade: 'C+', ch: 3.0 },
            { id: 402, name: 'ECO422', courseTitle: 'Development Economics', grade: 'C', ch: 3.0 },
            { id: 403, name: 'HRM413', courseTitle: 'Principles of Human Resource Management', grade: 'C', ch: 3.0 },
            { id: 404, name: 'ACT414', courseTitle: 'Business Taxation', grade: 'C', ch: 3.0 },
            { id: 405, name: 'ACT415', courseTitle: 'Principles of Cost Accounting', grade: 'D', ch: 3.0 },
            { id: 406, name: 'MKT416', courseTitle: 'Principles of Marketing', grade: 'D', ch: 3.0 },
        ]},
        // Semester 5 (Autumn 2023)
        { id: 5, name: 'Semester 5 (Autumn 2023)', isExpanded: false, subjects: [
            { id: 501, name: 'SM531', courseTitle: 'Strategic Marketing', grade: 'C+', ch: 3.0 },
            { id: 502, name: 'SC511', courseTitle: 'Speech Communication', grade: 'C', ch: 3.0 },
            { id: 503, name: 'BE521', courseTitle: 'Business Economics', grade: 'C', ch: 3.0 },
            { id: 504, name: 'FA551', courseTitle: 'Financial Accounting', grade: 'C', ch: 3.0 },
            { id: 505, name: 'EP561', courseTitle: 'Entrepreneurship', grade: 'C', ch: 3.0 },
            { id: 506, name: 'BM541', courseTitle: 'Business Mathematics-II', grade: 'D', ch: 3.0 },
        ]},
        // Semester 6 (Spring 2024)
        { id: 6, name: 'Semester 6 (Spring 2024)', isExpanded: false, subjects: [
            { id: 601, name: 'EC502', courseTitle: 'Environmental Sciences', grade: 'B+', ch: 3.0 },
            { id: 602, name: 'OB532', courseTitle: 'Organizational Behavior', grade: 'C', ch: 3.0 },
            { id: 603, name: 'BRM512', courseTitle: 'Business Research Methods', grade: 'C', ch: 3.0 },
            { id: 604, name: 'SI542', courseTitle: 'Statistical Inference', grade: 'D', ch: 3.0 },
            { id: 605, name: 'MAT552', courseTitle: 'Management Accounting Technique', grade: 'D', ch: 3.0 },
            { id: 606, name: 'IF522', courseTitle: 'Introduction to Finance', grade: 'D', ch: 3.0 },
        ]},
        // Semester 7 (Autumn 2024)
        { id: 7, name: 'Semester 7 (Autumn 2024)', isExpanded: false, subjects: [
            { id: 701, name: 'ACC671', courseTitle: 'Computerized Accounting', grade: 'B', ch: 3.0 },
            { id: 702, name: 'IB621', courseTitle: 'International Business', grade: 'C+', ch: 3.0 },
            { id: 703, name: 'RP641', courseTitle: 'Research Project', grade: 'C', ch: 3.0 },
            { id: 704, name: 'MA631', courseTitle: 'Managerial Accounting', grade: 'C', ch: 3.0 },
            { id: 705, name: 'AM611', courseTitle: 'Advanced Management', grade: 'B', ch: 3.0 },
            { id: 706, name: 'ACC851', courseTitle: 'Taxation', grade: 'B+', ch: 3.0 },
        ]},
        // Semester 8 (Spring 2025)
        { id: 8, name: 'Semester 8 (Spring 2025)', isExpanded: false, subjects: [
            { id: 801, name: 'CD801', courseTitle: 'Community Development', grade: 'B', ch: 3.0 },
            { id: 802, name: 'CL802', courseTitle: 'Corporate Law', grade: 'C', ch: 3.0 },
            { id: 803, name: 'MIS803', courseTitle: 'Management Information System', grade: 'C', ch: 3.0 },
            { id: 804, name: 'AA804', courseTitle: 'Advance Auditing', grade: 'C', ch: 3.0 },
            { id: 805, name: 'SM805', courseTitle: 'Strategic Management', grade: 'D', ch: 3.0 },
            { id: 806, name: 'FM806', courseTitle: 'Financial Management', grade: 'D', ch: 3.0 },
        ]},
    ];


    // Initializes 8 default semesters (as per typical 4-year degree)
    function initializeApp(reset = false) {
        if (semesters.length === 0 || reset) {
            // Deep copy the initial data to allow editing
            semesters = JSON.parse(JSON.stringify(initialSemesterData)); 
            semesterCount = 8;
        } 
        // Set default date for Proforma
        document.getElementById('report-date').textContent = new Date().toLocaleDateString('en-GB');
        renderSemesters();
        calculateAll(); // Run calculation on load for initial data population
    }

    // Handles input changes (Grade, CH, Name, Title)
    function handleInputChange(semesterId, subjectId, field, value) {
        const semester = semesters.find(s => s.id === semesterId);
        if (semester) {
            const subject = semester.subjects.find(s => s.id === subjectId);
            if (subject) {
                if (field === 'ch') {
                    // Ensure CH is treated as a number
                    subject[field] = parseFloat(value) || 0;
                } else {
                    subject[field] = value;
                }
                // For performance, we skip re-render and just call calculateAll
                calculateAll(); 
            }
        }
    }
    
    // Adds a new subject to a specific semester
    function addSubjectToSemester(semesterId) {
        const semester = semesters.find(s => s.id === semesterId);
        if (semester) {
            semester.subjects.push({
                id: Date.now() + Math.random(), 
                name: `NEW-SUB`,
                courseTitle: `New Subject ${semester.subjects.length + 1}`,
                grade: '',
                ch: 3.0
            });
            renderSemesters(); 
            calculateAll();
        }
    }
    
    // Removes a subject from a specific semester
    function removeSubjectFromSemester(semesterId, subjectId) {
        const semester = semesters.find(s => s.id === semesterId);
        if (semester) {
            console.log(`Removing subject ${subjectId} from semester ${semesterId}`);
            semester.subjects = semester.subjects.filter(s => s.id !== subjectId);
            renderSemesters(); 
            calculateAll();
        }
    }

    // --- Calculation Logic ---

    function calculateSemesterGPA(semester) {
        let totalQP = 0;
        let totalCH = 0;

        semester.subjects.forEach(subject => {
            const gradePoint = GRADE_MAP[subject.grade] || 0.0;
            const creditHours = subject.ch;

            if (creditHours > 0) {
                subject.product = gradePoint * creditHours;
                
                totalQP += subject.product;
                totalCH += creditHours;
            } else {
                subject.product = 0.00;
            }
        });

        const gpa = totalCH > 0 ? totalQP / totalCH : 0;

        return {
            totalQP: totalQP,
            totalCH: totalCH,
            gpa: gpa, // Semester GPA
        };
    }

    function calculateAll() {
        let cumulativeTotalQP = 0;
        let cumulativeTotalCH = 0;
        
        // 1. Update Student Details in Preview
        document.getElementById('report-enrollment').textContent = document.getElementById('enrollment-input').value;
        document.getElementById('report-name').textContent = document.getElementById('name-input').value;
        document.getElementById('report-father-name').textContent = document.getElementById('father-name-input').value;
        document.getElementById('report-exam-no').textContent = document.getElementById('exam-no-input').value;

        // 2. Calculate and store semester-wise and cumulative results
        semesters.forEach(semester => {
            const result = calculateSemesterGPA(semester);
            
            // Accumulate totals
            cumulativeTotalQP += result.totalQP;
            cumulativeTotalCH += result.totalCH;

            // Store results in semester object
            semester.gpa = result.gpa;
            semester.totalQP = result.totalQP;
            semester.cumulativeCGPA = cumulativeTotalCH > 0 ? cumulativeTotalQP / cumulativeTotalCH : 0;
        });
        
        // 3. Render the Proforma Preview (Report)
        renderProformaReport();

        // 4. Update the interactive accordions for GPA/CGPA display (for user feedback)
        semesters.forEach(semester => {
             const gpaDisplay = document.getElementById(`gpa-${semester.id}`);
             const cumulativeCGPADisplay = document.getElementById(`cumulative-cgpa-${semester.id}`);
             if(gpaDisplay) gpaDisplay.textContent = semester.gpa.toFixed(2);
             if(cumulativeCGPADisplay) cumulativeCGPADisplay.textContent = semester.cumulativeCGPA.toFixed(2);
        });
    }
    
    // --- UI Rendering Functions ---

    function toggleAccordion(semesterId) {
        const content = document.getElementById(`content-${semesterId}`);
        const icon = document.getElementById(`icon-${semesterId}`);
        const semester = semesters.find(s => s.id === semesterId);
        
        if (content && icon && semester) {
            const isCurrentlyHidden = content.classList.contains('hidden');
            
            if (isCurrentlyHidden) {
                content.classList.remove('hidden');
                icon.classList.add('rotated');
                semester.isExpanded = true;
            } else {
                content.classList.add('hidden');
                icon.classList.remove('rotated');
                semester.isExpanded = false;
            }
        }
    }

    // Renders the interactive input accordion (Left Panel)
    function renderSemesters() {
        semestersContainer.innerHTML = ''; 

        semesters.forEach(semester => {
            const semesterDiv = document.createElement('div');
            semesterDiv.className = 'bg-white p-3 shadow-md rounded-lg border border-gray-200';

            const header = document.createElement('div');
            header.className = 'flex justify-between items-center cursor-pointer p-1';
            header.onclick = () => toggleAccordion(semester.id);
            
            // Note: GPA/CGPA values are placeholders, updated by calculateAll()
            header.innerHTML = `
                <div class="flex items-center space-x-2">
                    <span class="text-lg font-bold text-gray-800">${semester.name}</span>
                </div>
                <div class="flex items-center space-x-3 text-sm font-bold text-right">
                    <span class="text-green-600">GPA: <span id="gpa-${semester.id}">0.00</span></span>
                    <span class="text-red-600">CGPA: <span id="cumulative-cgpa-${semester.id}">0.00</span></span>
                    <svg id="icon-${semester.id}" class="accordion-icon h-4 w-4 text-gray-500 ${semester.isExpanded ? 'rotated' : ''}" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" ></path></svg>
                </div>
            `;
            semesterDiv.appendChild(header);

            const content = document.createElement('div');
            content.id = `content-${semester.id}`;
            content.className = `accordion-content mt-3 ${semester.isExpanded ? '' : 'hidden'}`;
            
            // Subject Table Header (Input Panel)
            content.innerHTML += `
                <div class="flex text-xs font-semibold text-gray-500 mb-1 border-b pb-1">
                    <div class="w-[15%]">Code</div>
                    <div class="w-[55%]">Course Title</div>
                    <div class="w-[15%] text-center">Grade</div>
                    <div class="w-[15%] text-center">CH</div>
                    <div class="w-4"></div>
                </div>
            `;

            // Subject Rows
            semester.subjects.forEach(subject => {
                content.innerHTML += createSubjectRow(semester.id, subject);
            });

            // Add subject button
             content.innerHTML += `
                <button onclick="addSubjectToSemester(${semester.id})" class="mt-2 text-blue-500 hover:text-blue-700 text-xs font-semibold">
                    + Add New Subject
                </button>
            `;

            semesterDiv.appendChild(content);
            semestersContainer.appendChild(semesterDiv);
        });
    }

    // Renders the input row for each subject
    function createSubjectRow(semesterId, subject) {
        // Use a unique ID for the subject if one isn't present
        const subjectId = subject.id || (Date.now() + Math.random());
        return `
            <div class="flex items-center space-x-1 py-1 text-xs">
                <input type="text" value="${subject.name}" 
                       oninput="handleInputChange(${semesterId}, ${subjectId}, 'name', this.value)"
                       placeholder="Code" 
                       class="w-[15%] p-1 border border-gray-200 rounded-md input-focus">
                
                <input type="text" value="${subject.courseTitle}" 
                       oninput="handleInputChange(${semesterId}, ${subjectId}, 'courseTitle', this.value)"
                       placeholder="Title" 
                       class="w-[55%] p-1 border border-gray-200 rounded-md input-focus">

                <select onchange="handleInputChange(${semesterId}, ${subjectId}, 'grade', this.value)"
                        class="grade-select w-[15%] p-1 border border-gray-200 rounded-md bg-white input-focus text-center">
                    <option value="" ${!subject.grade ? 'selected' : ''}>-</option>
                    ${GRADE_POINTS.map(grade => 
                        `<option value="${grade}" ${subject.grade === grade ? 'selected' : ''}>${grade}</option>`
                    ).join('')}
                </select>
                
                <input type="number" value="${subject.ch.toFixed(1)}" min="1" step="0.5" 
                       onchange="handleInputChange(${semesterId}, ${subjectId}, 'ch', this.value)"
                       class="ch-input w-[15%] p-1 border border-gray-200 rounded-md text-center">

                <button onclick="removeSubjectFromSemester(${semesterId}, ${subjectId})" class="w-4 text-red-500 hover:text-red-700 transition">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3 mx-auto" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" ></path></svg>
                </button>
            </div>
        `;
    }

    // Renders the Marks Proforma Report for PDF generation (Right Panel)
    function renderProformaReport() {
        const reportContainer = document.getElementById('report-semesters-container');
        reportContainer.innerHTML = '';
        
        let cumulativeTotalQP = 0;
        let cumulativeTotalCH = 0;

        semesters.forEach((semester, index) => {
            const semesterResult = calculateSemesterGPA(semester); // Calculate current semester
            
            cumulativeTotalQP += semesterResult.totalQP;
            cumulativeTotalCH += semesterResult.totalCH;
            
            const currentCGPA = cumulativeTotalCH > 0 ? cumulativeTotalQP / cumulativeTotalCH : 0;
            
            // Extract the session year from the semester name string
            const match = semester.name.match(/\(([^)]+)\s(\d{4})\)/);
            const session = match ? `${match[1]} ${match[2]}` : 'N/A';

            let tableHTML = `
                <div class="text-[0.7rem] font-bold mb-1">
                    ${semester.name.split(' ')[0]} ${semester.name.split(' ')[1]} <span class="font-normal">(${session})</span>
                </div>
                <table class="proforma-table w-full border-collapse">
                    <thead>
                        <tr>
                            <th class="w-[10%]">Course Code</th>
                            <th class="w-[65%]">Course Title</th> <th class="w-[5%]">Grade</th>
                            <th class="w-[5%]">GP</th>
                            <th class="w-[7.5%]">Credit Hours</th>
                            <th class="w-[7.5%]">Product</th>
                        </tr>
                    </thead>
                    <tbody>
            `;

            semester.subjects.forEach(subject => {
                const gradePoint = GRADE_MAP[subject.grade] || 0.0;
                tableHTML += `
                    <tr>
                        <td class="text-left">${subject.name}</td>
                        <td class="text-left">${subject.courseTitle}</td>
                        <td class="text-center font-bold">${subject.grade || '-'}</td>
                        <td class="text-center">${gradePoint.toFixed(2)}</td>
                        <td class="text-center">${subject.ch.toFixed(2)}</td>
                        <td class="text-center">${subject.product.toFixed(2)}</td>
                    </tr>
                `;
            });

            tableHTML += `
                    </tbody>
                </table>
                <div class="flex justify-end text-[0.65rem] font-bold mt-1 space-x-4 pr-1">
                    <span>GPA: ${semesterResult.gpa.toFixed(2)}</span>
                    <span>CGPA: ${currentCGPA.toFixed(2)}</span>
                </div>
            `;
            
            reportContainer.innerHTML += tableHTML;
        });
    }

    // --- PDF Download Function ---
    window.jsPDF = window.jspdf.jsPDF; // Assign jsPDF to window scope for easy access

    function downloadPDF() {
        const input = document.getElementById('proforma-container');
        
        // Temporarily set a higher DPI for better PDF quality
        const scale = 3; // Increased scale factor for resolution

        // Add a temporary class to maximize print quality appearance
        input.classList.add('max-w-none'); 

        html2canvas(input, {
            scale: scale,
            useCORS: true,
            logging: false,
            // Ensure background colors are captured
            allowTaint: true
        }).then(canvas => {
            // Remove the temporary class
            input.classList.remove('max-w-none');

            const imgData = canvas.toDataURL('image/jpeg', 1.0); // Use JPEG for smaller size
            const pdf = new jsPDF('p', 'mm', 'a4'); // 'p' for portrait, 'mm' for units, 'a4' for size
            const pdfWidth = pdf.internal.pageSize.getWidth();
            const pdfHeight = pdf.internal.pageSize.getHeight();
            
            const imgProps = pdf.getImageProperties(imgData);
            const imgHeight = (imgProps.height * pdfWidth) / imgProps.width;
            
            let heightLeft = imgHeight;
            let position = 0;

            // Add image, handling multi-page output if needed
            pdf.addImage(imgData, 'JPEG', 0, 0, pdfWidth, imgHeight);
            heightLeft -= pdfHeight;

            while (heightLeft >= 0) {
                position = heightLeft - imgHeight;
                pdf.addPage();
                pdf.addImage(imgData, 'JPEG', 0, position, pdfWidth, imgHeight);
                heightLeft -= pdfHeight;
            }
            
            pdf.save(`FUUAST_Proforma_${document.getElementById('enrollment-input').value || 'Report'}.pdf`);
        }).catch(error => {
            console.error('PDF Download Failed:', error);
            // Fallback for user feedback (custom modal would be better)
            const fallbackMessage = document.createElement('div');
            fallbackMessage.textContent = 'PDF download failed. Check console for details.';
            fallbackMessage.className = 'fixed top-5 right-5 bg-red-500 text-white p-3 rounded-lg shadow-xl';
            document.body.appendChild(fallbackMessage);
            setTimeout(() => document.body.removeChild(fallbackMessage), 5000);
        });
    }

    // --- Utility Functions ---

    function clearAllData() {
        console.log("Data Resetting...");
        // Reset input fields to initial values
        document.getElementById('enrollment-input').value = 'IS/BS(Com)/A-20/M/A/736';
        document.getElementById('name-input').value = 'Sameer Ashraf';
        document.getElementById('father-name-input').value = 'Muhammad Ashraf';
        document.getElementById('exam-no-input').value = '0028497';

        // Reset semester data and re-initialize
        semesters = [];
        semesterCount = 0;
        initializeApp(true); 
    }

    window.onload = initializeApp;

</script>

</body>
</html>
