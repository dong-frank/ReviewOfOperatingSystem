# 信号量和PV操作的习题

## 一
四个进程Pi（i=0…3）和四个信箱Mj（j=0…3），进程间借助相邻信箱传递消息，即Pi每次从Mi中取一条消息，经加工后送入M[(i+1) mod 4]，其中M0、M1、M2、M3分别可存放3、3、2、2个消息。初始状态下，M0装了三条消息，其余为空。试以P、V操作为工具，写出Pi（i=0…3）的同步工作算法
```
semaphore mutex0, mutex1, mutex2, mutex3; // 四个信箱
mutex0 = 1;mutex1 = 1;mutex2 = 1;mutex3 = 1; // 初始为1,可用

semaphore empty0, empty1, empty2, empty3; //四个信箱的空闲信号量
empty0 = 0; //初始0号信箱装满了
empty1 = 3; //其余信箱为空
empty2 = 2;
empty3 = 2;

semaphore full0, full1, full2, full3; //四个信箱的满信号量
full0 = 3;
full1 = 0;
full2 = 0;
full3 = 0;

int indexIn0, indexIn1, indexIn2, indexIn3, indexOut0, indexOut1, indexOut2, indexOut3; //信箱中当前取或放的信息的下标
indexIn0 = indexIn1 = indexIn2 = indexIn3 = indexOut0 = indexOut1 = indexOut2 = indexOut3 = 0;

cobegin:

    process P0(){
        while(true){
            P(full0);
                P(mutex0);
                    getMessage(M0,indexOut0);
                    indexOut0 = (indexOut0 + 1) % 3;
                V(mutex0);
            V(empty0);
            handleMessage();
            P(empty1);
                P(mutex1);
                    putMessage(M1,indexIn1);
                    indexIn1 = (indexIn1 + 1) % 3;
                V(mutex1);
            V(full1);
        }
    }

    process P1(){
        while(true){
            P(full1);
                P(mutex1);
                    getMessage(M1,indexOut1);
                    indexOut1 = (indexOut1 + 1) % 3;
                V(mutex1);
            V(empty1);
            handleMessage();
            P(empty2);
                P(mutex2);
                    putMessage(M2,indexIn2);
                    indexIn2 = (indexIn2 + 1) % 2;
                V(mutex2);
            V(full2);
        }
    }

    process P2(){
        while(true){
            P(full2);
                P(mutex2);
                    getMessage(M2,indexOut2);
                    indexOut2 = (indexOut2 + 1) % 2;
                V(mutex2);
            V(empty2);
            handleMessage();
            P(empty3);
                P(mutex3);
                    putMessage(M3,indexIn3);
                    indexIn3 = (indexIn3 + 1) % 2;
                V(mutex3);
            V(full3);
        }
    }

        process P3(){
        while(true){
            P(full3);
                P(mutex3);
                    getMessage(M3,indexOut3);
                    indexOut3 = (indexOut3 + 1) % 2;
                V(mutex3);
            V(empty3);
            handleMessage();
            P(empty0);
                P(mutex0);
                    putMessage(M0,indexIn0);
                    indexIn0 = (indexIn0 + 1) % 3;
                V(mutex0);
            V(full0);
        }
    }

coend  
```

## 二
有一个阅览室，读者进入时必须先在一张登记表上登记，此表为每个座位列出一个表目，包括座位号、姓名，读者离开时要注销登记信息；假如阅览室共有100个座位。
```
semaphore tableMutex = 1;
semaphore freeSeat = 100;
struct {
    char name[15];
    int no;
}table[100];

for(int i=0; i<100; i++){
    table[i].no = i;
    table[i].name = "";
}

cobegin:

    process reader(char[] readerName){
        int seatNo = -1;
        P(freeSeat);
            P(tableMutex);
                seatNo = findAfreeSeat(table);
                table[seatNo].name = readerName;
            V(tableMutex);

            goToSeatAndRead(seatNo);

            P(tableMutex);
                table[seatNo].name = "";
            V(tableMutex);
        
        V(freeSeat);
    }

coend;
```

## 三
在一个盒子里，混装了数量相等的黑白围棋子。现在用自动分拣系统把黑子、白子分开，设分拣系统有二个进程P1和P2，其中P1拣白子；P2拣黑子。规定每个进程每次拣一子；当一个进程在拣时，不允许另一个进程去拣；当一个进程拣了一子时，必须让另一个进程去拣。
```
semaphore black = 1;
semaphore white = 0;

cobegin:
    process P1(){
        while(true){
            P(white);
                pickWhite();
            V(black);
        }
    }

    process P2(){
        while(true){
            P(black);
                pickBlack();
            V(white);
        }
    }

coend;
```

## 四
一组生产者进程和一组消费者进程共享9个缓冲区，每个缓冲区可以存放一个整数。生产者进程每次一次性地向3个缓冲区中写入整数，消费者进程每次从缓冲区取出一个整数。

```
semaphore producerMutex=1;
semaphore consumerMutex=1;
semaphore put = 3; //总共9个缓冲区，一次放3个，可以放3次
semaphore get = 0;
int indexIn=0;
int indexOut=0;
int getCount=0;

cobegin:
    produceri(){
        int numbers[3];
        numbers = produce();
        P(put);
        P(producerMutex);
            putInBuf(numbers[0],indexIn);
            indexIn = (indexIn + 1) % 9;
            putInBuf(numbers[1],indexIn);
            indexIn = (indexIn + 1) % 9;
            putInBuf(numbers[2],indexIn);
            indexIn = (indexIn + 1) % 9;
        V(producerMutex);
    V(get);
    V(get);
    V(get);
    }

    consumeri(){
        int number;
        P(get);
        P(consumerMutex);
            number = getOutOfBuf(indexOut);
            indexOut = (indexOut + 1) % 9;
            getCount++;
            if(getCount == 3){
                V(put);
                getCount = 0;
            }
        V(consumerMutex);
        consum(number);
    }
coend;

```

## 哲学家就餐问题

```
semaphore fork[5];
semaphore room = 4;
for(int i=0; i<5; i++){
    fork[i]=1;
}

cobegin

    process philosopher_i(){
        while(true){
            thinking();
            P(room);
            P(fork[i]);
            P(fork[(i+1) % 5]);
            eating();
            V(for[(i+1) % 5]);
            V(fork[i]);
            V(room);
        }
    }

coend
```


## 读者写者问题
**读写公平的实现**

```
semaphore rmutex=1;
semaphore wmutex=1;
semaphore mutex=1;
int read_count=0;

cobegin
    process read_i(){
        while(true){
            P(mutex);
            P(rmutex);
            if(read_count == 0){
                P(wmutex);
            }
            read_count++;
            V(rmutex);
            V(mutex);

            {read};

            P(rmutex);
            read_count--;
            if(read_count == 0){
                V(wmutex);
            }
            V(rmutex);
        }
    }

    process write_i(){
        while(true){
            P(mutex);
            P(wmutex);
            {write};
            V(wmutex);
            V(mutex);
        }
    }

coend
```

## 睡眠的理发师问题

```
int waiting_customers=0;
int CHAIRS = N;
semaphore barber=0;
semaphore customer=0;
semaphote mutex=1;

cobegin
    process customer_i(){
        P(mutex);
        if(waiting_customers < CHAIRS){
            waiting_customers++;
            V(customer);
            V(mutex);
            P(barber);
            {get_hair_cut};
        }else{
            V(mutex);
        }
    }

    process barber(){
        while(true){
            P(customer);
            P(mutex);
            waiting_customers--;
            V(barber);
            V(mutex);
            {cut_hair};
        }
    }

coend

```

## 农夫猎人问题

```
semaphore cage=1;
semaphore tiger=0;
semaphore sheep=0;

cobegin

    process farmer(){
        while(true){
            P(cage);
            {put_sheep};
            V(sheep);
        }
    }

    process hunter(){
        while(true){
            P(cage);
            {put_tiger};
            V(tiger);
        }
    }

    process restaurant(){
        while(true){
            P(sheep);
            {get_sheep};
            V(cage);
        }
    }

    process zoo(){
        while(true){
            P(tiger);
            {get_tiger};
            V(cage);
        }
    }

coend

```

## 银行业务问题

```
semaphore customer=0;
semaphore server=0;
semaphore mutex=1;

cobegin
    process customer_i(){
        {get_number};
        P(mutex);
        {find_wait_seat};
        V(mutex);
        V(customer);
        P(server);
    }

    process server_i(){
        while(true){
            P(customer);
            P(mutex);
            {customer_leave_wait};
            V(mutex);
            {server_the_customer};
            V(server);
        }
    }
coend

```

## 吸烟者问题

```
semaphore put=1;
semaphore get[3];
for(int i=0; i<3; i++){
    get[i] = 0;
}

cobegin

    process server(){
        while(true){
            P(put);
            int i=0;
            int j=0;
            while(i == j){
                i = Rand() %3;
                j = Rand() %3;
            }

            if((i==0 && j==1) || (i==1 && j==0)){
                {put_items};
                V(get[2]);
            }else if((i==0 && j==2) || (i==2 && j==0)){
                {put_items};
                V(get[1]);
            }else{
                {put_items};
                V(get[0]);
            }

        }
    }

    process smoker_i(){
        P(get[i]);
        {get_items};
        {smoke};
        V(put);
    }
coend
```


## 读木桥问题

```
semaphore bridge=1;
semaphore east=1;
semaphore west=1;
int east_count=0;
int west_count=0;

cobegin

    process east(){
        while(true){
            P(east);
            east_count++;
            if(east_count==1){
                P(bridge);
            }
            V(east);
            {过独木桥};

            P(east);
            east_count--;
            if(east_count==0){
                V(bridge);
            }
            V(east);
        }
    }

    process west(){
        while(true){
            P(west);
            west_count++;
            if(west_count==1){
                P(bridge);
            }
            V(west);
            {过独木桥};

            P(west);
            west_count--;
            if(west_count==0){
                V(bridge);
            }
            V(west);
        }
    }

coend
```

**限制桥上最多有k两车, 那么{过独木桥}要变为临界区**
```
semaphore bridge=1;
semaphore bridge_count=k;
semaphore east=1;
semaphore west=1;
int east_count=0;
int west_count=0;

cobegin

    process east(){
        while(true){
            P(east);
            east_count++;
            if(east_count==1){
                P(bridge);
            }
            V(east);
            P(bridge_count);
            {过独木桥};
            V(bridge_count);
            P(east);
            east_count--;
            if(east_count==0){
                V(bridge);
            }
            V(east);
        }
    }

    process west(){
        while(true){
            P(west);
            west_count++;
            if(west_count==1){
                P(bridge);
            }
            V(west);
            P(bridge_count);
            {过独木桥};
            V(bridge_count);

            P(west);
            west_count--;
            if(west_count==0){
                V(bridge);
            }
            V(west);
        }
    }

coend
```