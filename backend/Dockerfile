FROM node:22-alpine

WORKDIR /app

# Копируем package.json и package-lock.json
COPY package*.json ./

# Устанавливаем зависимости
RUN npm install

# Копируем исходный код
COPY server.js ./

# Создаем директорию для данных
RUN mkdir -p /app/data

# Открываем порт
EXPOSE 3000

# Запускаем приложение
CMD ["node", "server.js"]