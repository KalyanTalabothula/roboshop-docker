FROM node:20-alpine3.21 AS builder
WORKDIR /opt/server 
COPY package.json . 
COPY *.js .  
RUN npm install 
# upto here cheste manaki code vastumdhi, Directory lopala package.json copy chestunna
# then java script file copy chestunna and Dependencies ni install chestunna 
# ekkadi varuku naku kavalisina outputs vastunnaee i mean /opt/server lo

FROM node:20-alpine3.21 
RUN addgroup -S roboshop && adduser -S roboshop -G roboshop
# RUN addgroup -S roboshop 
# RUN adduser -S roboshop -G roboshop
ENV MONGO="true" \
    MONGO_URL="mongodb://mongodb:27017/catalogue"
WORKDIR /opt/server
USER roboshop
COPY --from=builder /opt/server /opt/server
# from yekkada nimdi copy chestunnamo dhani peru, yee folder copy chestunnam, yekkadiki copy chesunnam dhaniki peru yemi estunnam. 
CMD [ "node", "server.js" ]


# FROM node:20-alpine3.21
# # RUN addgroup -S appgroup && adduser -S appuser -G appgroup
# # addgroup roboshop && adduser roboshop ane user ni roboshop group ki add chey ani meaning, dheniki ite user create chesaro dhaniki undali. 
# RUN addgroup -S roboshop && adduser -S roboshop -G roboshop
# WORKDIR /opt/server
# RUN chown -R roboshop:roboshop /opt/server 
# # -R is for okavela directorys kani unte andhukee..
# USER roboshop
# COPY package.json .
# COPY *.js . 
# RUN npm install
# ENV MONGO="true" \
#     MONGO_URL="mongodb://mongodb:27017/catalogue"
# CMD [ "node", "server.js" ]