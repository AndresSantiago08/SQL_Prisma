# SQL_Prisma

*Objetivo: Usar Prisma DB para conectar una base de datos de PostgreSQL con un servidor Express.*    

- Instalar Prisma en un proyecto de js e inicializarlo.  
<pre><code>npm install prisma --save-dev
npx prisma init
</code></pre>

- Crear un modelo en `schema.prisma` para agregar una tabla a una base de datos ya existente en PostgreSQL.  
<pre><code>model Explorer {
  id Int @id @default(autoincrement())
  name String @unique
  username String @db.VarChar(255)
  mission String @db.VarChar(255)
  azureCertification Boolean @default(false)
  dateCreated DateTime @default(now())
  lastUpdated DateTime @updatedAt
}
</code></pre>

- Versionar la base de datos.
<pre><code>npx prisma migrate dev --name init
</code></pre>

- Crear un servidor de Express para leer, insertar, modificar y borar información en la base de datos.
<pre><code>const express = require('express');
const app = express();
app.use(express.json());
const port = process.env.PORT || 3000;

const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

app.get('/', (req, res) => {
  res.json({message: 'alive'});
});

app.get('/explorers', async (req, res) => {
    const allExplorers =  await prisma.explorer.findMany({});
    res.json(allExplorers);
});

app.get('/explorers/:id', async (req, res) => {
    const id = req.params.id;
    const explorer = await prisma.explorer.findUnique({where: {id: parseInt(id)}});
    res.json(explorer);
});

app.post('/explorers', async (req, res) => {
    const explorer = {
      name: req.body.name,
      username: req.body.username,
      mission: req.body.mission
     };
    const message = 'Explorer creado.';
    await prisma.explorer.create({data: explorer});
    return res.json({message});
});

app.put('/explorers/:id', async (req, res) => {
	const id = parseInt(req.params.id);

	await prisma.explorer.update({
		where: {
			id: id
		},
		data: {
			mission: req.body.mission
		}
	})

	return res.json({message: "Actualizado correctamente"});
});

app.delete('/explorers/:id', async (req, res) => {
	const id = parseInt(req.params.id);
	await prisma.explorer.delete({where: {id: id}});
	return res.json({message: "Eliminado correctamente"});
});

app.listen(port, () => {
  console.log(`Listening to requests on port ${port}`);
});
</code></pre>
---

*Resultado:*  

---

*Dependencias:*

- npm v8.10.0
- node v17.8.0
- prisma v3.14.0
- express v4.18.1

## Práctica propuesta por Carlo Gilmar 
