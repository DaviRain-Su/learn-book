# 在网络通信中发送结构体的总结
    
    在网络通信中发送直接发送结构体数据;
    
    规定双方以一个小火车的协议约定发送数据，解决粘包问题。

    typedef struct train{
        int datalen;
        char buf[1000];
    }train_t;
    
    上面时规定的小火车。

    发送结构体数据定义：

    struct Head {
        uint32_t preamble;
        uint32_t len;
        uint32_t ver;
        uint16_t flag;
        uint16_t type;
        
        Head(uint32_t preamble, uint32_t len, uint32_t ver, uint16_t flag, uint16_t type)
            : preamble(preamble),
            len(len),
            ver(ver),
            flag(flag),
            type(type)
        {}
    };

    这是一个定义了的client 与server端之发送的数据结构。
    
    // client 段发送结构体数据
    Head head(0xffffffff, 10, 0, 0, 0);

    train_t sendTrain;
    memset(&sendTrain, 0, sizeof(train_t));
    sendTrain.datalen = sizeof(Head);
    memcpy(sendTrain.buf, &head, sizeof(Head));

    //发送数据
    write(sockfd, (char*)&sendTrain, sizeof(Head) + sizeof(int));
    
    //server 端接受结构体数据

    int recvDatalen;
    int ret = read(sockfd, (char*)&recvDatalen, sizeof(int));
    if(-1 == ret){
        if(errno == EINTR){
            perror("read");
        }
    }

    char buff[65536] = {0}; 
    ret = read(sockfd, buff, recvDatalen);
    if(-1 == ret){
        if(errno == EINTR){
            perror("readn");
        }
    }

    //将数据全部保存到里面
    vector<char> result;
    result.reserve(recvDatalen);
    for(int i = 0; i != recvDatalen; i++){
        result.push_back(buff[i]);
    }

    // return string(buff); 这样是错误的在传递的不是字符串的时候
    将数据保存在vector中而不是string中，原因是保存在string中数据可能保存的不全。    
    这样就保障了一定可以接受道recvDatalen大小的数据。 


