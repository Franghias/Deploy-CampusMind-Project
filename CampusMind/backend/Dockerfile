# filepath: c:\Users\User\Desktop\Deploy-CampusMind-Project\CampusMind\backend\Dockerfile
# Use Node.js LTS as the base image
FROM node:slim

# Set the working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# # Expose the port your app runs on
# EXPOSE 3000

# Cloud Run expects the app to listen on $PORT
ENV PORT=8080
EXPOSE 8080


# Start the application
CMD ["node", "app.js"]