import { sql } from '@vercel/postgres';

export default async function handler(req, res) {
  // Solo aceptamos peticiones POST (cuando se envía el formulario)
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Método no permitido. Usa POST.' });
  }

  try {
    // Extraemos los datos que envía tu menú
    const { client, items, total, type } = req.body;

    // Validación básica de seguridad
    if (!client || !client.name || !items || items.length === 0) {
      return res.status(400).json({ error: 'Datos del pedido incompletos.' });
    }

    // 1. Nos aseguramos de que la tabla exista (se creará sola la primera vez)
    await sql`
      CREATE TABLE IF NOT EXISTS pedidos_esquinita (
        id SERIAL PRIMARY KEY,
        nombre VARCHAR(255) NOT NULL,
        telefono VARCHAR(50),
        direccion TEXT,
        tipo_entrega VARCHAR(50),
        monto_total DECIMAL(10, 2),
        articulos JSONB,
        fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP
      );
    `;

    // 2. Insertamos la nueva orden en tu base de datos
    const result = await sql`
      INSERT INTO pedidos_esquinita (nombre, telefono, direccion, tipo_entrega, monto_total, articulos)
      VALUES (
        ${client.name}, 
        ${client.phone}, 
        ${client.address}, 
        ${type}, 
        ${total}, 
        ${JSON.stringify(items)}
      )
      RETURNING id;
    `;

    // Respondemos con éxito y el ID generado
    return res.status(200).json({ 
      success: true, 
      mensaje: 'Pedido guardado en la base de datos',
      orden_id: result.rows[0].id 
    });

  } catch (error) {
    console.error('Error al guardar en PostgreSQL:', error);
    return res.status(500).json({ error: 'Error interno del servidor al procesar la orden.' });
  }
}
