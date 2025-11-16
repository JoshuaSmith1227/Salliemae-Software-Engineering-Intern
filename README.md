# Sallie Mae Summer 2025 Internship: Production LLM Systems

**Technical summary of production AI/ML engineering work at Sallie Mae (May 2025 – August 2025)**

*Code is proprietary and remains internal to Sallie Mae. This documentation demonstrates the scope, technical challenges, and engineering solutions delivered during the internship.*

---

## Project 1: AI-Powered Scholarship Classification at Scale

**Challenge:** Automate categorization of 12,000+ scholarships for Sallie Mae's scholarship search platform ([sallie.com/scholarships/scholly](https://www.sallie.com/scholarships/scholly))

**Technology:** Python, OpenAI API, concurrent processing (ThreadPoolExecutor)

**Status:** Shipped to production, currently serving thousands of users

### The Performance Optimization Journey

**Initial Baseline (Naive Implementation):**
- Sequential processing: 12,000+ individual API calls
- Estimated runtime: **200+ hours** (completely impractical for production)
- Estimated cost: ~$240 in API fees per run
- Blocking deployment due to time/cost constraints

**Engineering Problem:** How to reduce runtime by 99%+ while maintaining accuracy?

### Multi-Stage Optimization Approach

**Stage 1: Batch Processing**
- Grouped multiple scholarships per API call using structured prompts
- Reduced total API calls from 12,000+ → ~1,200
- **Estimated Result:** ~20 hours runtime (10x improvement)
- **Tradeoff:** Increased prompt complexity, managed response parsing

**Stage 2: Concurrent Processing**
- Implemented `ThreadPoolExecutor` for parallel API requests
- Added rate limiting to respect OpenAI API constraints
- Managed thread-safe error handling and retry logic
- **Estimated Result:** ~2 hours runtime (100x improvement from baseline)
- **Learning:** I/O-bound operations are ideal candidates for concurrency

**Stage 3: Model & Prompt Optimization**
- Switched to faster OpenAI model after cost/accuracy analysis
- Refined prompt engineering for more efficient token usage
- Implemented intelligent caching for repeated classification patterns
- **Final Result:** ~1 hour runtime (**200x improvement from baseline**)
- **Estimated Production Cost:** Reduced to ~$60 per run

### Technical Implementation Highlights

**Concurrency Architecture (Conceptual):**
```python
# Pseudocode - actual implementation is proprietary
from concurrent.futures import ThreadPoolExecutor
import threading

def classify_batch(scholarship_batch):
    # Rate limiting to respect API constraints
    with rate_limit_control:
        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",  # or similar fast model
            messages=[{"role": "system", "content": classification_prompt},
                      {"role": "user", "content": format_batch(scholarship_batch)}]
        )
    return parse_classifications(response)

# Parallel execution with controlled concurrency
with ThreadPoolExecutor(max_workers=10) as executor:
    futures = [executor.submit(classify_batch, batch) 
               for batch in scholarship_batches]
    results = [f.result() for f in futures]
```

**Production Considerations:**
- Error handling with retry logic for API failures
- Rate limit management for API constraints
- Comprehensive logging for debugging and monitoring
- Progress tracking with resumption capability
- Data validation to ensure classification quality
- Cost monitoring and budget controls

### Business Impact
- **User Experience:** Real-time scholarship categorization on live website
- **Operational Efficiency:** Automated previously manual classification process
- **Cost Optimization:** Estimated 4x reduction in API costs per run
- **Scalability:** Production-ready tool that can handle database growth
- **Accuracy:** 90% classification accuracy validated against manual review

### Key Technical Learnings
1. **Concurrency Patterns:** ThreadPoolExecutor is essential for I/O-bound tasks like API calls
2. **Batch Processing:** Grouping requests reduces latency and cost overhead
3. **Cost-Performance Tradeoffs:** Model selection significantly impacts both speed and budget
4. **Production Engineering:** Error handling, monitoring, and graceful degradation are critical
5. **Iterative Optimization:** Systematic profiling and measurement drive improvement

---

## Project 2: College ROI Calculator

**Goal:** Full-stack web application for prospective students to compare college costs and earnings

**Technology:** HTML/CSS/JavaScript frontend, Python data pipeline

**Data Scale:** 4,500+ U.S. colleges with cost, salary, and financial aid data

**Key Contributions:**
- Developed Python data ingestion pipeline parsing multiple data sources
- Implemented frontend/backend logic for user inputs (school, major, financial aid, housing)
- Designed user experience based on iterative feedback
- Delivered multi-year ROI projections with salary progression modeling

### Full-Stack Application Demo Summary

A final video demonstration confirmed the tool was **fully shipped and functional**. The demo showcased:

- **Polished UI/UX:** A clean, professional, two-column interface for data entry.
- **Dynamic Inputs:** Smart autocomplete for "University" and "Major" fields, alongside dropdowns for "Financial Aid" and "Housing".
- **Data-Driven Visualization:** The results page displayed projected **Year 1 and Year 10 salaries**, a **bar chart** illustrating ROI over 15 years, and dynamic **school comparisons** with other top programs.
- **Functional Logic:** The tool successfully generated unique, logical outputs for different user inputs (e.g., CMU/ECE vs. Henderson State/Liberal Arts).

**Relevance:** This project demonstrates full-stack capabilities and data pipeline engineering, complementing the AI/ML focus.

---

## Skills Demonstrated

**AI/ML Engineering:**
- Production LLM integration (OpenAI API)
- Prompt engineering and optimization
- Model selection and cost analysis
- Performance optimization at scale

**Performance Engineering:**
- Systematic optimization methodology (profile → optimize → measure)
- Concurrency patterns for I/O-bound workloads
- API rate limiting and resource management
- Scalability considerations for production systems

**Python Expertise:**
- ThreadPoolExecutor and concurrent programming
- API integration with error handling and retry logic
- Data processing with json/csv libraries
- Production-grade code quality

**Production Readiness:**
- Shipped code to live production systems
- Error handling and recovery mechanisms
- Monitoring, logging, and debugging
- Cost optimization and budget management

---


*This internship provided hands-on experience with production-scale LLM systems, performance optimization, and full-stack development. The scholarship classification tool demonstrates the systems thinking and optimization mindset essential for building efficient AI applications.*
