# Result Processing & Tabulation System

A complete full-stack solution for processing, calculating, and exporting student results with support for linked/paired subjects, automatic ranking, and PDF generation.

## Features

- ✅ **Comprehensive Grading System**: A+ to F grading with grade point mapping
- ✅ **Linked Subjects Support**: Handle paired subjects (e.g., English 1st + English 2nd) as one subject
- ✅ **Automatic Result Calculation**: Server-side computation with transaction support
- ✅ **Ranking System**: Automatic new roll assignment based on performance
- ✅ **PDF Export**: Professional tabulation sheets and individual student result PDFs
- ✅ **RESTful API**: Complete API endpoints for integration
- ✅ **Modern UI**: Bootstrap 5 frontend with responsive design

## Technology Stack

- **Backend**: Node.js with Express.js
- **Database**: MySQL (mysql2)
- **PDF Generation**: Puppeteer
- **Frontend**: Bootstrap 5, Vanilla JavaScript
- **Template Engine**: EJS (for PDF templates)

## Prerequisites

- Node.js (v14 or higher)
- MySQL (v5.7 or higher) or MariaDB
- npm or yarn

## Installation

1. **Clone or download the project**

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure database**
   
   Create a `.env` file in the root directory:
   ```env
   DB_HOST=localhost
   DB_USER=root
   DB_PASSWORD=your_password
   DB_NAME=school_results
   DB_PORT=3306
   PORT=3000
   NODE_ENV=development
   ```

4. **Initialize database**
   ```bash
   npm run init-db
   ```
   
   This will:
   - Create the database if it doesn't exist
   - Create all tables with proper indexes and foreign keys
   - Insert sample data (Class 9 with 10 students)

5. **Start the server**
   ```bash
   npm start
   ```
   
   For development with auto-reload:
   ```bash
   npm run dev
   ```

6. **Access the application**
   - Web Interface: http://localhost:3000
   - API Base URL: http://localhost:3000/api

## Database Schema

### Tables

1. **classes**: Class information
   - `id`, `class_name`, `created_at`

2. **subjects**: Subject details with linked subject support
   - `id`, `class_id`, `subject_name`, `full_marks`, `grading_type`, `linked_subject_id`

3. **students**: Student information
   - `id`, `class_id`, `roll`, `name`, `old_roll`, `new_roll`

4. **marks**: Student marks for each subject
   - `id`, `student_id`, `subject_id`, `exam`, `marks`, `total_marks`, `grade`

## Grading System

### Grade Ranges
- **A+**: 80% - 100% (Grade Point: 5.0)
- **A**: 70% - 79% (Grade Point: 4.0)
- **A-**: 60% - 69% (Grade Point: 3.5)
- **B**: 50% - 59% (Grade Point: 3.0)
- **C**: 40% - 49% (Grade Point: 2.0)
- **D**: 33% - 39% (Grade Point: 1.0)
- **F**: 0% - 32% (Grade Point: 0)

### Final Grade Calculation
- Average GP → Letter Grade mapping:
  - ≥ 4.75 → A+
  - ≥ 3.75 → A
  - ≥ 3.25 → A-
  - ≥ 2.75 → B
  - ≥ 1.75 → C
  - ≥ 0.75 → D
  - < 0.75 → F

## Result Calculation Logic

1. **Linked Subjects**: Paired subjects are combined:
   - Combined marks = marks1 + marks2
   - Combined total = total1 + total2
   - Combined grade calculated from combined percentage

2. **Failure Detection**: If any subject (single or combined) has grade 'F', student fails overall.

3. **Final Calculation** (for passed students):
   - Total marks = sum of all subject marks (combined for pairs)
   - Total GP = sum of all subject GPs
   - Final GP = Total GP / Subject Count
   - Final Grade = convert GP to letter

4. **Ranking**:
   - Only passed students are ranked
   - Sort by: Final GP (DESC) → Total Marks (DESC) → Old Roll (ASC)
   - Assign new_roll sequentially starting from 1

## API Endpoints

### Subjects
- `GET /api/:classId/subjects` - Get all subjects for a class

### Students
- `GET /api/:classId/students` - Get all students for a class

### Results
- `GET /api/:classId/results?exam=FINAL` - Get computed results (JSON)
- `POST /api/:classId/generate-results?exam=FINAL` - Generate and save results

### PDF Export
- `GET /api/:classId/tabulation-pdf?exam=FINAL` - Download tabulation sheet PDF
- `GET /api/:classId/student/:studentId/result-pdf?exam=FINAL` - Download individual student result PDF

## Usage Examples

### Generate Results via API
```bash
curl -X POST "http://localhost:3000/api/4/generate-results?exam=FINAL"
```

### Get Results JSON
```bash
curl "http://localhost:3000/api/4/results?exam=FINAL"
```

### Download Tabulation PDF
```bash
curl "http://localhost:3000/api/4/tabulation-pdf?exam=FINAL" -o tabulation.pdf
```

## Sample Data

The initialization script includes:
- **Class 9** with 10 students
- **10 subjects** including:
  - Bangla 1st Paper, Bangla 2nd Paper
  - English 1st Paper + English 2nd Paper (linked pair)
  - Mathematics, Physics, Chemistry, Biology
  - Social Science, ICT
- **Sample marks** for FINAL exam with various performance levels:
  - Excellent students (A+)
  - Average students (A, A-, B)
  - Failed students (one with Mathematics F, one with English combined F)

## Testing

### Test Grading Functions
```javascript
const { calculateGrade, gradeToPoint, pointToLetter } = require('./utils/grading');

// Test grade calculation
console.log(calculateGrade(85, 100)); // A+
console.log(calculateGrade(65, 100)); // A-
console.log(calculateGrade(30, 100)); // F

// Test GP conversion
console.log(gradeToPoint('A+')); // 5.0
console.log(gradeToPoint('F')); // 0

// Test GP to letter
console.log(pointToLetter(4.8)); // A+
console.log(pointToLetter(3.5)); // A-
```

### Test with Sample Data

1. Start the server: `npm start`
2. Open http://localhost:3000
3. Go to "Generate Results"
4. Enter Class ID: `4`, Exam: `FINAL`
5. Click "Generate Results"
6. View results and download PDFs

## Project Structure

```
.
├── config/
│   └── database.js          # Database configuration
├── controllers/
│   ├── pdfController.js     # PDF generation endpoints
│   ├── resultController.js  # Result calculation endpoints
│   ├── studentController.js # Student endpoints
│   └── subjectController.js # Subject endpoints
├── database/
│   └── init.sql             # Database initialization SQL
├── public/
│   ├── index.html           # Home page
│   ├── generate-results.html # Result generation page
│   └── view-results.html    # Results preview page
├── routes/
│   └── api.js               # API routes
├── scripts/
│   └── init-db.js          # Database initialization script
├── services/
│   ├── pdfService.js        # PDF generation service
│   └── resultService.js     # Result calculation service
├── utils/
│   ├── grading.js           # Grading utility functions
│   └── linkedSubjects.js    # Linked subjects utilities
├── .env.example             # Environment variables template
├── .gitignore
├── package.json
├── README.md
└── server.js                # Main server file
```

## Important Notes

1. **Linked Subjects**: When linking subjects, set `linked_subject_id` on the first subject. The system automatically handles the pairing.

2. **Transactions**: Result generation uses database transactions to ensure consistency when assigning new rolls.

3. **Failed Students**: Students with grade 'F' in any subject:
   - Final grade = 'F'
   - Total marks and GP are hidden (displayed as '—')
   - No new_roll assigned
   - Excluded from ranking

4. **PDF Generation**: Uses Puppeteer which requires Chromium. On first run, it will download Chromium automatically.

## Troubleshooting

### Database Connection Error
- Verify MySQL is running
- Check `.env` file credentials
- Ensure database user has proper permissions

### PDF Generation Fails
- Ensure Puppeteer dependencies are installed
- On Linux, may need additional dependencies: `sudo apt-get install -y gconf-service libasound2`

### Port Already in Use
- Change `PORT` in `.env` file
- Or stop the process using port 3000

## License

ISC

## Support

For issues or questions, please check the code comments or create an issue in the repository.

