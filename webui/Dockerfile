FROM node
RUN npm install redis
RUN npm install express
COPY webui.js .
COPY files /files

ENTRYPOINT ["node"]
CMD ["webui.js"]
#CMD node webui.js
