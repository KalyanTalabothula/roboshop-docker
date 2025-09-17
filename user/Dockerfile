FROM node20
WORKDIR /opt/server
ENV MONGO="true" \
    REDIS_URL="redis://redis:6379" \
    MONGO_URL="mongodb://mongodb:27017/users"

COPY package.json .
COPY *.js . 
RUN npm install

CMD [ "node", "server.js" ]

# Developer frequently ee package.json & .js files ee rondu marite Obvisoly dependencies kuda marataii kabatti, ela Frequently change ayye vii manam last lo pedatam. 

# ENV MONGO="true" 
# ENV REDIS_URL="redis://redis:6379" 
# ENV MONGO_URL="mongodb://mongodb:27017/users"

# ekkada ( ENV ) Environment instructions lo ela echham, appudu number of LAYERS & TIME perugutumdhi..
