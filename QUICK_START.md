# Quick Start Guide

## Setup (5 minutes)

1. **Install dependencies**
   ```bash
   npm install
   ```

2. **Configure database** (create `.env` file)
   ```env
   DB_HOST=localhost
   DB_USER=root
   DB_PASSWORD=your_password
   DB_NAME=school_results
   DB_PORT=3306
   PORT=3000
   ```

3. **Initialize database**
   ```bash
   npm run init-db
   ```

4. **Start server**
   ```bash
   npm start
   ```

5. **Open browser**
   ```
   http://localhost:3000
   ```

## Test the System

1. Go to **Generate Results** page
2. Enter:
   - Class ID: `4`
   - Exam: `FINAL`
3. Click **Generate Results**
4. View the results table
5. Go to **View Results** page
6. Download PDFs:
   - Tabulation Sheet (all students)
   - Individual Student Results

## Sample Data Included

- **Class 9** with 10 students
- **10 subjects** including English 1st + English 2nd (linked pair)
- **Sample marks** with various grades (A+ to F)

## Key Features to Test

1. **Linked Subjects**: Check English 1st + English 2nd are combined
2. **Failure Detection**: Student 4 (Mohammad Hasan) failed Mathematics
3. **Ranking**: Check new_roll assignment (failed students excluded)
4. **PDF Export**: Download and verify PDF formatting

## API Testing

```bash
# Get results
curl http://localhost:3000/api/4/results?exam=FINAL

# Generate results
curl -X POST http://localhost:3000/api/4/generate-results?exam=FINAL

# Download tabulation PDF
curl http://localhost:3000/api/4/tabulation-pdf?exam=FINAL -o tabulation.pdf
```

## Troubleshooting

- **Database error**: Check MySQL is running and `.env` credentials
- **Port in use**: Change PORT in `.env`
- **PDF fails**: Ensure Puppeteer dependencies installed

