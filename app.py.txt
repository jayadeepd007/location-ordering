from flask import Flask, request, jsonify
from supabase import create_client, Client

app = Flask(__name__)

# Supabase Configuration
SUPABASE_URL = 'https://your-supabase-url.supabase.co'
SUPABASE_KEY = 'your-anon-key'
supabase: Client = create_client(SUPABASE_URL, SUPABASE_KEY)

@app.route('/book', methods=['POST'])
def book():
    data = request.json
    name = data.get('name')
    date = data.get('date')
    time = data.get('time')
    contact = data.get('contact')

    if not all([name, date, time, contact]):
        return jsonify({"error": "All fields are required"}), 400

    try:
        # Insert data into Supabase
        result = supabase.table('bookings').insert({
            'name': name,
            'date': date,
            'time': time,
            'contact': contact
        }).execute()

        return jsonify({"message": "Booking successful", "data": result.data}), 201
    except Exception as e:
        return jsonify({"error": str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)
