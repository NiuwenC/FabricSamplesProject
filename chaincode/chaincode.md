## 网络中安装chaincode的过程

(1) 打包代码:

    peer lifecycle chaincode package ${CC_NAME}.tar.gz --path ${CC_SRC_PATH} --lang ${CC_RUNTIME_LANGUAGE} 
    --label ${CC_NAME}_${CC_VERSION} >&log.txt
    
(2) peer1和peer2上安装chaincode

    # 需要确定环境变量
    peer lifecycle chaincode install ${CC_NAME}.tar.gz >&log.txt
    
(3) 查询安装结果

    peer lifecycle chaincode queryinstalled
    
(4) 批准chaincode定义

    peer lifecycle chaincode approveformyorg -o localhost:7050 
    --ordererTLSHostnameOverride orderer.example.com --tls 
    --cafile $ORDERER_CA --channelID $CHANNEL_NAME --name ${CC_NAME} 
    --version ${CC_VERSION} --package-id ${PACKAGE_ID} --sequence ${CC_SEQUENCE} ${INIT_REQUIRED} ${CC_END_POLICY} 
    ${CC_COLL_CONFIG} >&log.txt
    
(5) 检查是否全部批准

    peer lifecycle chaincode checkcommitreadiness --channelID $CHANNEL_NAME --name ${CC_NAME} --version ${CC_VERSION} --sequence ${CC_SEQUENCE} ${INIT_REQUIRED} ${CC_END_POLICY} ${CC_COLL_CONFIG} --output json >&log.txt
       
(6) 提交chaincode

    peer lifecycle chaincode commit -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile $ORDERER_CA --channelID $CHANNEL_NAME --name ${CC_NAME} $PEER_CONN_PARMS --version ${CC_VERSION} --sequence ${CC_SEQUENCE} ${INIT_REQUIRED} ${CC_END_POLICY} ${CC_COLL_CONFIG} >&log.txt

(7) 查询提交

    peer lifecycle chaincode querycommitted --channelID $CHANNEL_NAME --name ${CC_NAME} >&log.txt
    
(8) 如果配置了cci 就是初始化函数 则进行初始化 -cci initLedger

    peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile $ORDERER_CA -C $CHANNEL_NAME -n ${CC_NAME} $PEER_CONN_PARMS --isInit -c ${fcn_call} >&log.txt
    
