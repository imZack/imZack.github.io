title: Gitlab 連接 LDAP uid 變動處理
date: 2017-11-30 22:46:06
tags:
- Gitlab
- LDAP
---

這有點久之前發生的事了，不過還是紀錄一下留個參考。

故事是這樣...Gitlab 7 啟用 LDAP 功能讓每位同事都能透過公司的帳號密碼登入。有一天組織變動後，居然怎麼樣都無法登入了！囧rz...

尋尋覓覓找到照篇討論 [Existing users cannot login with ldap accounts
](https://gitlab.com/gitlab-org/gitlab-ce/issues/946)

1. 進入 Gitlab console 模式 `sudo -u git RAILS_ENV=production /opt/gitlab/bin/gitlab-rails console`
2. 執行下面這些東東，基本上就是手動修改。把 UID、部門資料之類的東西更新成最新的值
```ruby
for item in Identity.find_each
    if item.extern_uid.include? "應用軟體課"
        item.extern_uid = item.extern_uid.sub("應用軟體課", "應用系統課")
        puts item.to_json()
        item.save()
    end
end

Identity.update(3, extern_uid: "CN=AAA xxx(xxxx),OU=aaa,OU=軟體研發部,OU=共用平台,OU=yyy,OU=222,DC=xxx,DC=com")
```

過了這麼久，該 Issue 還是開著呢。
