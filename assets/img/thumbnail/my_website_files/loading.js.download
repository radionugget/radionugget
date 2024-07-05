const messages = [
    { img: "/assets/img/loading/loading1.webp", text: "En orbite dans un instant..." },
    { img: "/assets/img/loading/loading2.webp", text: "Calcul des trajectoires..." },
    { img: "/assets/img/loading/loading3.webp", text: "Connexion à l'univers..." }
];

const randomMessage = messages[Math.floor(Math.random() * messages.length)];

document.addEventListener('DOMContentLoaded', function() {
    document.getElementById('loadingImage').src = randomMessage.img;
    document.getElementById('loadingText').innerText = randomMessage.text;
});