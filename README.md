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
    


具体过程:

1:

    source env_1.sh
    peer lifecycle chaincode package fabcar.tar.gz --path ../chaincode/fabcar/javascript/ --lang node --label fabcar_1.0

2:

    source env_1.sh
    peer lifecycle chaincode install fabcar.tar.gz
    
    source env_2.sh
    peer lifecycle chaincode install fabcar.tar.gz
    
3:

    peer lifecycle chaincode queryinstalled 
    
4: 
    
    source env_1.sh
    peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
     --channelID mychannel --name fabcar --version 1 --package-id  fabcar_1:28a00dceeb981fff81dc71219b3e6ba3cc17579596d815f59b7d81245a6df578 --sequence 1 --init-required

5:
    
    source env_1.sh
    peer lifecycle chaincode checkcommitreadiness --channelID mychannel --name fabcar --version 1 --sequence 1 --init-required --output json
    
6:

    peer lifecycle chaincode commit -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --channelID mychannel --name fabcar --peerAddresses localhost:7051  --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt --version 1 --sequence 1 --init-required
    
7:

    peer lifecycle chaincode querycommitted --channelID mychannel --name fabcar
    
8:

    peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile /opt/hyperledger/fabric/fabricimages/fabric-samples/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n fabcar --peerAddresses localhost:7051 --tlsRootCertFiles /opt/hyperledger/fabric/fabricimages/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles /opt/hyperledger/fabric/fabricimages/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt --isInit -c '{"function":"initLedger","Args":[]}'
    
    
9: 更新:

    打包
    批准
    安装  version  sequence +1
    检查安装
    提交
    调用
    

高层应用

    (1) 命令查询
    peer chaincode query -C mychannel -n fabcar -c '{"Args":["queryCars"]}'
    

在目前的基础之上,进行其他操作
 
       
    
    
    