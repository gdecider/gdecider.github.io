---
title: Одновременная авторизация только одного пользователя на сайте
keywords: ограничение авторизации битрикс, проверка количества авторизаций, только один пользователь битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-single-user-auth.html
summary: Ограничение количества пользователей авторизованных под одним аккаунтом в разных браюзерах на сайте под управлением CMS Битрикс
toc: false
---

### Задача

Требуется ограничить количество одновременно авторизованных под одним и тем же аккаунтом пользователей на сайте. Нужно это для того, чтобы при доступе, например к платному контенту пользователь не мог дать доступ к своему аккаунту другу и они одновременно могли пользоаться этим контентом без дополнительной оплаты.

### Решение

Для решения поставленной задачи нам нужно: 

* Добавить пользовательское поля для сущности USER, в котором мы будем хранить код сессии пользователя.
* Добавить обработчик события авторизации пользователя (OnAfterUserAuthorize), который будет заполнять поле с ключем сессии
* Добавить обработчик подключения пролога, в котором будем проверять совпадение ключа сессии и в случае ошибки выкидывать пользователя из системы.

```php
<?php
// в файле обработчиков событий, который подключаем в init.php
AddEventHandler('main', 'OnBeforeProlog', ['CUserLoginChecker', 'checkAuth']);
AddEventHandler('main', 'OnAfterUserAuthorize', ['CUserLoginChecker', 'saveSID']);

// класс с обработчиками
class CUserLoginChecker
{
    public function saveSID()
    {
        global $USER;
        $userId = $USER->GetID();
        if ($userId && $userId > 0) {
            $USER->Update($userId, [
                'UF_CURRENT_SESSION' => $USER->GetSessionHash()
            ]);
        }
    }

    public function checkAuth()
    {
        global $USER;
        if ($USER->IsAuthorized()) {
            $userFields = CUser::GetByID($USER->GetID())->Fetch();
            $oldSession = $userFields['UF_CURRENT_SESSION'];
            if ($oldSession !== $USER->GetSessionHash()) {
                $USER->Logout();
            }
        }
    }
}
```
