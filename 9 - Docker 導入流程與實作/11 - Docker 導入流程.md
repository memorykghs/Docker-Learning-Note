# 11 - Docker 導入流程
1. 確認應用程式使用的程式語言與版本
2. 參考相同程式語言的Dockerfile寫法
3. 列出應用程式相依的套件(包含系統、語言、字型等等)
4. 列出應用程式的設定(環境變數、設定檔)
5. 撰寫Dockerfile並測試
6. 檢查是否有檔案不該加入映像檔中( `.dockerignore`)
   例如：`.env` 或是 docker-compose file，因為有時候可能會放一些 credential 在裏面
7. 列出應用程式相依的服務(Database、Redis)
8. 確認服務啟動順序並撰寫Docker Compose yaml