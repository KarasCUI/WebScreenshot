#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
# 注意: 以下 为了使用 Selenium
# 复制 tools/selenium/ 文件夹下的文件到 ./tools/selenium/ 即 /app/tools/selenium/
COPY tools/selenium/ ./tools/selenium/
RUN apt update
RUN apt-get install unzip
RUN apt-get install gdebi-core -y
WORKDIR /app/tools/selenium
# 安装 Chrome
# 注意: Chrome 版本必须与 chromedriver 版本对应
# 安装 google-chrome*.deb 包依赖
# 注意: gdebi 没有 -y, 使用 -n 代替
RUN gdebi -n google-chrome*.deb
# 安装 chromedriver
RUN unzip chromedriver_linux64.zip
# 为所有用户添加可执行权限 (对chromedriver文件)
RUN chmod a+x chromedriver
# 下面两行安装中文字体
RUN apt install -y --force-yes --no-install-recommends fonts-wqy-microhei
RUN apt install -y --force-yes --no-install-recommends ttf-wqy-zenhei
# 以下安装 Selenium WebDriver 需要的依赖
RUN apt-get install libglib2.0 -y
RUN apt-get install libnss3-dev -y
RUN apt-get install libxcb1 -y

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["src/WebScreenshot/WebScreenshot.csproj", "src/WebScreenshot/"]
RUN dotnet restore "src/WebScreenshot/WebScreenshot.csproj"
COPY . .
WORKDIR "/src/src/WebScreenshot"
RUN dotnet build "WebScreenshot.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "WebScreenshot.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WebScreenshot.dll"]
