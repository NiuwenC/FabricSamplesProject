## 商业票据服务

1 打包

    peer lifecycle chaincode package cp.tar.gz --lang node --path ./contract --label cp_0
    
2 安装

    peer lifecycle chaincode install cp.tar.gz
    
3

    peer lifecycle chaincode queryinstalled
    
4 
    
    export PACGAGE_ID=cp_0:xxxx
    peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
         --channelID mychannel --name papercontract --version 1 --package-id  fabcar_1:xxxx --sequence 1 --tls --cafile $ORDERER_CA
         
5  Install and approve the smart contract as DigiBank 安装DigiBank

    source digibank.sh
    peer lifecycle chaincode package cp.tar.gz --lang node --path ./contract --label cp_0
    peer lifecycle chaincode install cp.tar.gz
    peer lifecycle chaincode queryinstalled
    export PACKAGE_ID=cp_0:xx
    peer lifecycle chaincode approveformyorg --orderer localhost:7050 --ordererTLSHostnameOverride orderer.example.com 
    --channelID mychannel --name
    papercontract -v 0 --package-id $PACKAGE_ID --sequence 1 --tls --cafile $ORDERER_CA
    

    
