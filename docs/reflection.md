# Engineering Reflection

## 1. Bottleneck

The main bottleneck in the baseline system was retrieval. The baseline Hit Rate (k=5) was only 11.11%, and the MRR was 0.06. This means the correct Treasury Bulletin source file was rarely retrieved in the top 5 results.

Because the generator depends on retrieved context, poor retrieval caused poor generation results. The baseline system had 0.00% groundedness, 0.00% factual accuracy, and a 100.00% hallucination rate.

The engineered system improved retrieval significantly, but generation was still difficult because many OfficeQA questions required table-based calculations, values spread across multiple chunks, or information from more than one source file.

## 2. Metadata Fix

I added metadata to each chunk, including source file, year, month, chunk ID, and system type. At first, I considered filtering by both year and month, but this was too strict because the date mentioned in the question did not always match the Treasury Bulletin publication month.

For example, March 31, 2025 data could appear in the June 2025 bulletin. Because of this, I changed the engineered retriever to use year-based filtering instead of strict year-month filtering.

I also added special handling for calendar-year and year-end questions by allowing the next year’s bulletin to be included. This helped because year-end data is often reported in the following year’s bulletin.

For the engineered generator context, I added neighbor/source expansion because some Treasury Bulletin tables were split across chunks. This helped recover values for table-based calculation questions such as the ESF total assets question and the FY2021 corporate income tax H-spread question.

## 3. Scaling Insight

If this system were scaled from the 2020–2025 subset to the full 1939–2025 Treasury Bulletin archive, the first component likely to become slow or brittle would be retrieval over the much larger vector database.

The full archive would create many more chunks, making embedding generation, storage, and search more expensive. It would also increase the risk of retrieving similar but incorrect tables from the wrong year.

To improve scalability, I would use stronger metadata filtering, table-aware chunking, batch embedding, persistent vector storage, a reranking step, and context expansion for adjacent chunks. I would also avoid storing raw downloaded data in GitHub and instead keep the notebook reproducible through the Hugging Face download process.
