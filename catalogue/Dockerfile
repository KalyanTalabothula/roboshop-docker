FROM node:20-alpine3.21
# RUN addgroup -S appgroup && adduser -S appuser -G appgroup
# addgroup roboshop && adduser roboshop ane user ni roboshop group ki add chey ani meaning, dheniki ite user create chesaro dhaniki undali. 
RUN addgroup -S roboshop && adduser -S roboshop -G roboshop
WORKDIR /opt/server
RUN chown -R roboshop:roboshop /opt/server 
# -R is for okavela directorys kani unte andhukee..
USER roboshop
COPY package.json .
COPY *.js . 
RUN npm install
ENV MONGO="true" \
    MONGO_URL="mongodb://mongodb:27017/catalogue"
CMD [ "node", "server.js" ]