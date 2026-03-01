import math

class Vehicle:
    def __init__(self, model, price_per_km, location):
        self._model = model
        self._price_per_km = price_per_km
        self._location = location
        self._available = True
        
        # Advanced rating system
        self._total_rating = 5.0
        self._rating_count = 1

    def is_available(self):
        return self._available

    def set_availability(self, status):
        self._available = status

    def get_location(self):
        return self._location

    def get_rating(self):
        return self._total_rating / self._rating_count

    def calculate_price(self, distance):
        return distance * self._price_per_km

    def update_rating(self, new_rating):
        if 1 <= new_rating <= 5:
            self._total_rating += new_rating
            self._rating_count += 1

    def distance_to(self, user_location):
        x1, y1 = self._location
        x2, y2 = user_location
        return math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2)

    def get_model(self):
        return self._model


class Taxi(Vehicle):
    def __init__(self, model, price_per_km, location):
        super().__init__(model, price_per_km, location)

class Boat(Vehicle):
    def __init__(self, model, price_per_km, location):
        super().__init__(model, price_per_km, location)

    def sea_info(self):
        return " Sea journey has started!"



class VipCar(Vehicle):
    def __init__(self, model, price_per_km, location, vip_multiplier=1.5):
        super().__init__(model, price_per_km, location)
        self._vip_multiplier = vip_multiplier

    # Override price calculation
    def calculate_price(self, distance):
        return distance * self._price_per_km * self._vip_multiplier

    def vip_service(self):
        return "VIP service activated!"


class VehicleManager:
    def __init__(self, vehicles):
        self.vehicles = vehicles

    def find_best_vehicle(self, user_location):
        available = [v for v in self.vehicles if v.is_available()]

        if not available:
            return None

        return min(
            available,
            key=lambda v: (
                v.distance_to(user_location),
                -v.get_rating()
            )
        )

class OperationResult:
    def __init__(self, message, obj=None, success=False):
        self.message = message
        self.obj = obj
        self.success = success



class Ride:
    def __init__(self, manager, user_location, distance):
        self.manager = manager
        self.user_location = user_location
        self.distance = distance
        self.vehicle = None

    def start(self):
        self.vehicle = self.manager.find_best_vehicle(self.user_location)

        if not self.vehicle:
            return OperationResult("❌ No available vehicle.", None, False)

        self.vehicle.set_availability(False)
        return OperationResult("✅ Vehicle found.", self, True)

    def finish(self, rating):
        self.vehicle.update_rating(rating)
        self.vehicle.set_availability(True)


taxi1 = Taxi("Toyota Corolla", 15, (5, 5))
boat1 = Boat("Yamaha 250", 25, (2, 3))
vip1 = VipCar("Mercedes S-Class", 40, (1, 1), 2.0)

manager = VehicleManager([taxi1, boat1, vip1])

ride = Ride(manager, (2, 2), 10)
result = ride.start()

if result.success:
    ride.finish(5)

    vehicle = ride.vehicle

    print(" Vehicle Found!")
    print(f"Type: {vehicle.__class__.__name__}")
    print(f"Model: {vehicle.get_model()}")
    print(f"Rating: {vehicle.get_rating():.2f}")
    print(f"Distance: {ride.distance} km")
    print(f"Price: {vehicle.calculate_price(ride.distance)} TL")

    # Special type checks
    if isinstance(vehicle, Boat):
        print(vehicle.sea_info())

    if isinstance(vehicle, VipCar):
        print(vehicle.vip_service())

else:
    print(result.message)
    # vehicle-manager
