# **Persona-Driven PDF Analysis System – CLI Guide**

## **Overview**

This tool analyzes PDF documents and extracts contextually relevant sections based on your **persona** and **job-to-be-done**, delivering ranked results with adaptive headings tailored to your specific needs.

---

## **Quick Start**

### **Option 1: Interactive Mode (Recommended)**

```bash
# Linux / macOS
./run_cli.sh

# Windows
run_cli.bat

# Or directly using Python
python cli_offline.py
```

### **Option 2: Command-Line Arguments**

```bash
python cli_offline.py \
  --persona "HR Professional" \
  --job "Streamline employee onboarding" \
  --input ./pdfs
```

---

## **Installation Options**

### ✅ **1. Standalone (No External Dependencies)**

`cli_offline.py` works in **demo mode** without additional installations, providing sample output for quick testing.

### ✅ **2. Full Functionality (Requires Dependencies)**

To enable full PDF analysis and semantic ranking:

```bash
pip install PyMuPDF pdfplumber pandas numpy scikit-learn sentence-transformers
```

### ✅ **3. Docker (Recommended for Production)**

```bash
# Build and run web interface
docker-compose up

# Run CLI mode
docker-compose --profile cli up pdf-analysis-cli
```

---

## **Usage Examples**

### **Basic Usage**

```bash
# Interactive mode (prompts for persona and job)
python cli_offline.py --input ./documents

# Fully specified via CLI
python cli_offline.py \
  --input ./reports \
  --persona "Data Analyst" \
  --job "Extract quarterly performance metrics" \
  --output ./analysis_results
```

### **Processing Multiple Files**

```bash
python cli_offline.py \
  --files report1.pdf report2.pdf presentation.pdf \
  --persona "Project Manager" \
  --job "Create project status dashboard"
```

### **Custom Output Formats**

```bash
# JSON only
python cli_offline.py --format json --persona "Student" --job "Prepare for exam"

# Summary only
python cli_offline.py --format summary --persona "Analyst" --job "Investment research"

# Both (default)
python cli_offline.py --format both --persona "HR" --job "Policy updates"
```

---

## **Command-Line Options**

| Option      | Short | Description                              | Example                           |
| ----------- | ----- | ---------------------------------------- | --------------------------------- |
| `--input`   | `-i`  | Input directory or single PDF file       | `--input ./documents`             |
| `--files`   | `-f`  | Specific PDF files to process            | `--files doc1.pdf doc2.pdf`       |
| `--output`  | `-o`  | Output directory (default: `./output`)   | `--output ./results`              |
| `--persona` | `-p`  | Your role/persona                        | `--persona "HR Professional"`     |
| `--job`     | `-j`  | Your job-to-be-done                      | `--job "Create onboarding forms"` |
| `--format`  |       | Output format: `json`, `summary`, `both` | `--format summary`                |
| `--quiet`   | `-q`  | Minimal output                           | `--quiet`                         |

---

## **Persona-Based Examples**

### **HR Professional**

```bash
python cli_offline.py \
  --persona "HR Professional" \
  --job "Create and manage fillable forms for employee onboarding"
```

**Expected Output:** Sections focused on workflow automation, compliance, digital form management, and e-signature integration.

---

### **Graduate Student**

```bash
python cli_offline.py \
  --persona "Graduate Student" \
  --job "Prepare comprehensive study materials for exams"
```

**Expected Output:** Key concepts, practice questions, and simplified explanations.

---

### **Data Analyst**

```bash
python cli_offline.py \
  --persona "Business Data Analyst" \
  --job "Extract actionable insights for quarterly business review"
```

**Expected Output:** Performance metrics, trends, data visualizations, and strategic insights.

---

## **Output Structure**

The system produces structured **JSON output** containing:

* **Metadata:** Document details, processing summary, timestamps
* **Extracted Sections:** Ranked by relevance with persona-adapted titles
* **Subsection Analysis:** Detailed breakdown with actionable insights
* **Relevance Scores:** Based on semantic similarity and keyword matching

### **Sample Output**

```json
{
  "metadata": {
    "persona": "HR Professional",
    "job_to_be_done": "Streamline onboarding process",
    "total_documents": 3,
    "processing_time": 4.2
  },
  "extracted_sections": [
    {
      "document": "handbook.pdf",
      "page_number": 15,
      "original_section_title": "New Employee Setup",
      "persona_adapted_title": "Digital Onboarding Workflow Optimization",
      "importance_rank": 1,
      "relevance_scores": {
        "total_score": 0.89
      },
      "content_preview": "Automated workflows can reduce onboarding time...",
      "actionable_insights": [
        "Implement e-signature integration",
        "Create automated form routing system"
      ]
    }
  ]
}
```

---

## **Project Structure**

```
project/
├── cli_offline.py        # CLI script
├── run_cli.sh           # Linux/Mac runner
├── run_cli.bat          # Windows runner
├── input/               # PDF input directory
├── output/              # Analysis results
├── Dockerfile           # Container config
└── docker-compose.yml   # Multi-container setup
```

---

## **Troubleshooting**

### **No PDFs Found**

* Ensure PDFs are placed in `./input` or use `--input` to specify.
* Verify `.pdf` extension and file permissions.

### **Missing Dependencies**

* For demo mode: No extra steps required.
* For full features:

  ```bash
  pip install -r requirements.txt
  ```
* Or use Docker for isolated setup.

### **Output Issues**

* Check write permissions in output directory.
* Use `--output` for a custom location.
* Use `--quiet` for minimal output.

---

## **Performance Notes**

* **CPU-Optimized:** Suitable for CPU-only environments.
* **Memory-Efficient:** Processes documents incrementally.
* **Fast Execution:** Designed for <60s processing of 3–5 documents.
* **Offline Capable:** Works without internet after setup.

---

## **Advanced Usage**

### **Batch Processing**

```bash
for dir in reports_q1 reports_q2 reports_q3; do
  python cli_offline.py --input $dir --output results_$dir --persona "Analyst" --job "Quarterly review"
done
```

### **Integration with Other Tools**

Convert output to CSV:

```bash
python -c "
import json, csv
with open('output/analysis_results.json') as f:
    data = json.load(f)
with open('sections.csv', 'w', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=['title','document','score'])
    writer.writeheader()
    for section in data['extracted_sections']:
        writer.writerow({
            'title': section['persona_adapted_title'],
            'document': section['document'],
            'score': section['relevance_scores']['total_score']
        })
"

