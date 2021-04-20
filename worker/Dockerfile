FROM python
RUN pip install redis
RUN pip install requests
COPY worker.py .

ENTRYPOINT ["python"]
CMD ["worker.py"]
#CMD python worker.py
