
# Catatan Harianku



###### 06 Januari 2026

## Cara Add Table baru dengan ASP .NET

```bash
dotnet ef database //nama migrations nanti nya
```
## Cara untuk menambahkan table

```bash 
# 1. Buat Migration
dotnet ef migrations add CreateNewTable

# 2. Upload ke Database (Gunakan ini agar tidak error socket)
dotnet ef database update --connection "Host=localhost;Port=5432;Database=devhabit;Username=postgres;Password=localhost"
```

## Cara untuk selesai mengedit field

```bash 
# 1. Buat Migration (Nama harus beda tiap kali add)
dotnet ef migrations add UpdateColumnName

# 2. Upload ke Database
dotnet ef database update --connection "Host=localhost;Port=5432;Database=devhabit;Username=postgres;Password=localhost"
```
