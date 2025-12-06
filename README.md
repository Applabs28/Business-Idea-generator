# Business-Idea-generator
Get business ideas and free marketing strategies.
## **Backend (Node/Express)**
- API routes for generating business ideas using OpenAI
- Stripe subscription checkout session
- Environment variables handling
- CORS setup to connect with frontend
- Example data returned so frontend works immediately


**Example `backend/server.js`:**
```js
const express = require('express');
const cors = require('cors');
const dotenv = require('dotenv');
const Stripe = require('stripe');
const axios = require('axios');


dotenv.config();
const stripe = Stripe(process.env.STRIPE_SECRET_KEY);
const app = express();
const port = process.env.PORT || 5000;


app.use(cors());
app.use(express.json());


// Business Idea Endpoint
app.post('/api/generate', async (req, res) => {
const { industry, budget } = req.body;


try {
const response = await axios.post('https://api.openai.com/v1/chat/completions', {
model: 'gpt-4',
messages: [
{ role: 'system', content: 'You are a business idea generator.' },
{ role: 'user', content: `Generate a startup idea in the ${industry} industry with a ${budget} budget.` }
]
}, {
headers: {
'Authorization': `Bearer ${process.env.OPENAI_API_KEY}`
}
});


const idea = response.data.choices[0].message.content;
res.json({ idea });
} catch (error) {
console.error(error);
res.status(500).json({ error: 'Failed to generate business idea' });
}
});


// Stripe Checkout Endpoint
app.post('/api/checkout', async (req, res) => {
const session = await stripe.checkout.sessions.create({
payment_method_types: ['card'],
mode: 'subscription',
line_items: [{ price: process.env.STRIPE_PRICE_ID, quantity: 1 }],
success_url: `${process.env.FRONTEND_URL}/success`,
cancel_url: `${process.env.FRONTEND_URL}/cancel`
});
res.json({ url: session.url });
});


app.listen(port, () => console.log(`Backend running on port ${port}`));
```


---


## **Frontend (React)**
FRONTEND_URL=https://your-frontend.vercel.app
