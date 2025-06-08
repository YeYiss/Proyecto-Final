# Proyecto-Final
import { useState } from 'react'
import { Button } from "/components/ui/button"
import { Card, CardContent, CardHeader, CardTitle, CardDescription, CardFooter } from "/components/ui/card"
import { Input } from "/components/ui/input"
import { Label } from "/components/ui/label"
import { User, Mail, Calendar, Clock, Heart } from "lucide-react"

type UserData = {
  email: string
  name: string
  age: string
  weight: string
  height: string
  dietType: string
}

type MealRecord = {
  id: string
  date: string
  time: string
  mealType: string
  description: string
  calories: string
}

export default function SaludVitalApp() {
  const [currentView, setCurrentView] = useState<'register' | 'personal' | 'food'>('register')
  const [userData, setUserData] = useState<UserData>({
    email: '',
    name: '',
    age: '',
    weight: '',
    height: '',
    dietType: 'balanced'
  })
  const [mealRecords, setMealRecords] = useState<MealRecord[]>([])
  const [newMeal, setNewMeal] = useState<Omit<MealRecord, 'id'>>({
    date: new Date().toISOString().split('T')[0],
    time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
    mealType: 'breakfast',
    description: '',
    calories: ''
  })

  const handleUserDataChange = (e: React.ChangeEvent<HTMLInputElement | HTMLSelectElement>) => {
    const { name, value } = e.target
    setUserData(prev => ({ ...prev, [name]: value }))
  }

  const handleMealChange = (e: React.ChangeEvent<HTMLInputElement | HTMLSelectElement>) => {
    const { name, value } = e.target
    setNewMeal(prev => ({ ...prev, [name]: value }))
  }

  const addMealRecord = () => {
    if (!newMeal.description || !newMeal.calories) return
    
    setMealRecords(prev => [
      ...prev,
      {
        ...newMeal,
        id: Date.now().toString()
      }
    ])
    
    setNewMeal({
      date: new Date().toISOString().split('T')[0],
      time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
      mealType: 'breakfast',
      description: '',
      calories: ''
    })
  }

  const getWeeklyReport = () => {
    const oneWeekAgo = new Date()
    oneWeekAgo.setDate(oneWeekAgo.getDate() - 7)
    
    const weeklyMeals = mealRecords.filter(meal => {
      const mealDate = new Date(meal.date)
      return mealDate >= oneWeekAgo
    })
    
    const totalCalories = weeklyMeals.reduce((sum, meal) => sum + Number(meal.calories || 0), 0)
    const avgDailyCalories = totalCalories / 7
    
    return {
      totalMeals: weeklyMeals.length,
      totalCalories,
      avgDailyCalories: isNaN(avgDailyCalories) ? 0 : avgDailyCalories.toFixed(0)
    }
  }

  const weeklyReport = getWeeklyReport()

  return (
    <div className="min-h-screen bg-gray-50 py-8 px-4">
      <div className="max-w-3xl mx-auto">
        <Card className="mb-8">
          <CardHeader>
            <CardTitle className="flex items-center gap-2">
              <Heart className="text-red-500" />
              Salud Vital
            </CardTitle>
            <CardDescription>
              Registra y controla tu información de salud y alimentación
            </CardDescription>
          </CardHeader>
          <CardContent>
            <div className="flex gap-4 mb-6">
              <Button 
                variant={currentView === 'register' ? 'default' : 'outline'} 
                onClick={() => setCurrentView('register')}
              >
                Registro
              </Button>
              <Button 
                variant={currentView === 'personal' ? 'default' : 'outline'} 
                onClick={() => setCurrentView('personal')}
                disabled={!userData.email}
              >
                Datos Personales
              </Button>
              <Button 
                variant={currentView === 'food' ? 'default' : 'outline'} 
                onClick={() => setCurrentView('food')}
                disabled={!userData.email}
              >
                Alimentación
              </Button>
            </div>

            {currentView === 'register' && (
              <div className="space-y-4">
                <div className="flex items-center gap-2">
                  <Mail className="text-gray-500" />
                  <Label htmlFor="email">Correo Electrónico</Label>
                </div>
                <Input
                  id="email"
                  name="email"
                  type="email"
                  placeholder="tu@email.com"
                  value={userData.email}
                  onChange={handleUserDataChange}
                />
                <Button 
                  className="w-full mt-4" 
                  onClick={() => setCurrentView('personal')}
                  disabled={!userData.email}
                >
                  Continuar
                </Button>
              </div>
            )}

            {currentView === 'personal' && (
              <div className="space-y-4">
                <div className="flex items-center gap-2">
                  <User className="text-gray-500" />
                  <Label htmlFor="name">Nombre Completo</Label>
                </div>
                <Input
                  id="name"
                  name="name"
                  placeholder="Tu nombre"
                  value={userData.name}
                  onChange={handleUserDataChange}
                />

                <div className="grid grid-cols-3 gap-4">
                  <div className="space-y-2">
                    <Label htmlFor="age">Edad</Label>
                    <Input
                      id="age"
                      name="age"
                      type="number"
                      placeholder="30"
                      value={userData.age}
                      onChange={handleUserDataChange}
                    />
                  </div>
                  <div className="space-y-2">
                    <Label htmlFor="weight">Peso (kg)</Label>
                    <Input
                      id="weight"
                      name="weight"
                      type="number"
                      placeholder="70"
                      value={userData.weight}
                      onChange={handleUserDataChange}
                    />
                  </div>
                  <div className="space-y-2">
                    <Label htmlFor="height">Estatura (cm)</Label>
                    <Input
                      id="height"
                      name="height"
                      type="number"
                      placeholder="170"
                      value={userData.height}
                      onChange={handleUserDataChange}
                    />
                  </div>
                </div>

                <div className="space-y-2">
                  <Label htmlFor="dietType">Tipo de Alimentación</Label>
                  <select
                    id="dietType"
                    name="dietType"
                    className="flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background file:border-0 file:bg-transparent file:text-sm file:font-medium placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50"
                    value={userData.dietType}
                    onChange={handleUserDataChange}
                  >
                    <option value="balanced">Balanceada</option>
                    <option value="vegetarian">Vegetariana</option>
                    <option value="vegan">Vegana</option>
                    <option value="keto">Keto</option>
                    <option value="lowcarb">Baja en carbohidratos</option>
                  </select>
                </div>

                <div className="flex justify-between mt-6">
                  <Button variant="outline" onClick={() => setCurrentView('register')}>
                    Atrás
                  </Button>
                  <Button onClick={() => setCurrentView('food')}>
                    Guardar y Continuar
                  </Button>
                </div>
              </div>
            )}

            {currentView === 'food' && (
              <div className="space-y-6">
                <Card>
                  <CardHeader>
                    <CardTitle>Nuevo Registro de Comida</CardTitle>
                  </CardHeader>
                  <CardContent className="space-y-4">
                    <div className="grid grid-cols-2 gap-4">
                      <div className="space-y-2">
                        <Label htmlFor="date">Fecha</Label>
                        <Input
                          id="date"
                          name="date"
                          type="date"
                          value={newMeal.date}
                          onChange={handleMealChange}
                        />
                      </div>
                      <div className="space-y-2">
                        <Label htmlFor="time">Hora</Label>
                        <Input
                          id="time"
                          name="time"
                          type="time"
                          value={newMeal.time}
                          onChange={handleMealChange}
                        />
                      </div>
                    </div>

                    <div className="space-y-2">
                      <Label htmlFor="mealType">Tipo de Comida</Label>
                      <select
                        id="mealType"
                        name="mealType"
                        className="flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background file:border-0 file:bg-transparent file:text-sm file:font-medium placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50"
                        value={newMeal.mealType}
                        onChange={handleMealChange}
                      >
                        <option value="breakfast">Desayuno</option>
                        <option value="lunch">Almuerzo</option>
                        <option value="dinner">Cena</option>
                        <option value="snack">Snack</option>
                      </select>
                    </div>

                    <div className="space-y-2">
                      <Label htmlFor="description">Descripción</Label>
                      <Input
                        id="description"
                        name="description"
                        placeholder="Qué comiste?"
                        value={newMeal.description}
                        onChange={handleMealChange}
                      />
                    </div>

                    <div className="space-y-2">
                      <Label htmlFor="calories">Calorías (kcal)</Label>
                      <Input
                        id="calories"
                        name="calories"
                        type="number"
                        placeholder="300"
                        value={newMeal.calories}
                        onChange={handleMealChange}
                      />
                    </div>
                  </CardContent>
                  <CardFooter>
                    <Button className="w-full" onClick={addMealRecord}>
                      Agregar Comida
                    </Button>
                  </CardFooter>
                </Card>

                <Card>
                  <CardHeader>
                    <CardTitle>Reporte Semanal</CardTitle>
                  </CardHeader>
                  <CardContent>
                    <div className="grid grid-cols-3 gap-4 text-center">
                      <div>
                        <p className="text-2xl font-bold">{weeklyReport.totalMeals}</p>
                        <p className="text-sm text-gray-500">Comidas registradas</p>
                      </div>
                      <div>
                        <p className="text-2xl font-bold">{weeklyReport.totalCalories}</p>
                        <p className="text-sm text-gray-500">Calorías totales</p>
                      </div>
                      <div>
                        <p className="text-2xl font-bold">{weeklyReport.avgDailyCalories}</p>
                        <p className="text-sm text-gray-500">Promedio diario</p>
                      </div>
                    </div>
                  </CardContent>
                </Card>

                <Card>
                  <CardHeader>
                    <CardTitle>Historial de Comidas</CardTitle>
                  </CardHeader>
                  <CardContent>
                    {mealRecords.length === 0 ? (
                      <p className="text-center text-gray-500 py-4">No hay registros aún</p>
                    ) : (
                      <div className="space-y-4">
                        {mealRecords.map(meal => (
                          <div key={meal.id} className="border rounded-lg p-4">
                            <div className="flex justify-between">
                              <div>
                                <p className="font-medium capitalize">{meal.mealType}</p>
                                <p className="text-gray-600">{meal.description}</p>
                              </div>
                              <div className="text-right">
                                <p className="font-bold">{meal.calories} kcal</p>
                                <p className="text-sm text-gray-500 flex items-center gap-1 justify-end">
                                  <Calendar className="h-3 w-3" />
                                  {meal.date}
                                  <Clock className="h-3 w-3 ml-2" />
                                  {meal.time}
                                </p>
                              </div>
                            </div>
                          </div>
                        ))}
                      </div>
                    )}
                  </CardContent>
                </Card>
              </div>
            )}
          </CardContent>
        </Card>
      </div>
    </div>
  )
}
