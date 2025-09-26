<img width="1368" height="233" alt="Screenshot from 2025-09-26 19-19-06" src="https://github.com/user-attachments/assets/c9cdb3e4-d20e-49e1-bda0-14a1155389ec" />
FROM python:3.11-slim
WORKDIR /app
# Copy requirements first to leverage caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
# Copy application code
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]

