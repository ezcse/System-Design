# Parking Lot System - Low Level Design (LLD) in C++

## System Requirements
- The parking lot should have multiple floors where customers can park their cars.
- The parking lot should have multiple entry and exit points.
- Customers can collect a parking ticket from the entry points and can pay the parking fee at the exit points on their way out.
- Customers can pay for the tickets at the automated exit panel or to the parking attendant.
- Customers can pay via both cash and credit cards.
- Customers should also be able to pay the parking fee at the customer’s info portal on each floor. If the customer has paid at the info portal, they don’t have to pay at the exit.
- The system should not allow more vehicles than the maximum capacity of the parking lot. If the parking is full, the system should be able to show a message at the entrance panel and on the parking display board on the ground floor.
- Each parking floor will have many parking spots. The system should support multiple types of parking spots such as Compact, Large, Handicapped, Motorcycle, etc.
- The Parking lot should have some parking spots specified for electric cars. These spots should have an electric panel through which customers can pay and charge their vehicles.
- The system should support parking for different types of vehicles like car, truck, van, motorcycle, etc.
- Each parking floor should have a display board showing any free parking spot for each spot type.
- The system should support a per-hour parking fee model. For example, customers have to pay $4 for the first hour, $3.5 for the second and third hours, and $2.5 for all the remaining hours.



### ParkingLot
Represents the entire parking lot which contains multiple floors and manages the parking and unparking of vehicles.

```cpp

#include<bits/stdc++.h>
using namespace std;

enum PaymentType{
	CASH,
	UPI,	
};

enum ParkingSpotType{
	Bike,
	Car,
	Truck,
	Van,
};

class ElectricVechicle
{
	int slots;
	PaymentType pay;
};

class Vehicle
{
private:	
	string vehicleNumber;
	string entryTime;
	string exitTime;
	PaymentType payMethod;
	bool paid;

public:
	Vehicle(string vehicleNum){
		vehicleNumber = vehicleNum;
		entryTime = "entryTime";
		paid = false;
		payMethod = PaymentType::UPI;
	}
	
	string GetVehicleNumber()
	{
	    return vehicleNumber;
	}

};

class ParkingSpot
{

private:
	bool isAvailable;
	Vehicle* parkedVehicle;

public:
	ParkingSpot(){
		isAvailable = true;
		parkedVehicle = NULL;
	}

	bool ParkVehicle(Vehicle *vehicle)
	{
		if(isAvailable)
		{
			parkedVehicle = vehicle;
			isAvailable = false;
			cout<<"Vechile number:"<<vehicle->GetVehicleNumber()<<" is PARKED successfully"<<endl;
			return true;
		}
		return false;
	}

	bool UnparkVehicle(Vehicle *vehicle)
	{
		if(parkedVehicle == vehicle)
		{
			isAvailable = true;
			parkedVehicle = NULL;
			cout<<"Vechile number:"<<vehicle->GetVehicleNumber()<<" is UN-PARKED successfully"<<endl;
			return true;
		}
		
		return false;
	}

	bool SpaceAvailable()
	{
		return isAvailable;
	}
};


class ParkingFloor
{
private:
	int totalSpots;
	int usedSpots;
	ParkingSpotType spotType;
	vector<ParkingSpot*> mParkingSpots;


public:
	ParkingFloor(int capacity, ParkingSpotType type)
	{
		totalSpots = capacity;
		spotType = type;
		usedSpots = 0;

		for(int i=0; i<capacity; i++)
			mParkingSpots.push_back(new ParkingSpot());
	}

	bool ParkVehicle(Vehicle* vehicle, ParkingSpotType spot)
	{
		if(spot == spotType)
		{
		    if(getFreeSpots() > 0)
		    {
    			for(auto &itr: mParkingSpots)
    			{
    				if(itr->SpaceAvailable()){
    					itr->ParkVehicle(vehicle);
    					usedSpots++;
    					return true;
    				}
    			}
		    }
		    else
		        cout<<"Parking Floor is FULL"<<endl;
		}
		return false;
	}

	bool UnparkVehicle(Vehicle *vehicle, ParkingSpotType spot)
	{	
		for(auto &itr: mParkingSpots)
		{
			if((spot == spotType) && (itr->UnparkVehicle(vehicle) == true)){
				usedSpots--;
				return true;
			}
		}
		return false;
	}

	int getFreeSpots()
	{
		return totalSpots - usedSpots;
	}

	ParkingSpotType getSpotType(){
		return spotType;
	}

};


class ParkingLotSystem {

private:
	int capacity;
	int maxFloors;
	int parkedVehicle;
	string parkingLotDescription;
	vector<ParkingFloor*> mParkingFloor;

public: 

	ParkingLotSystem(int floors, vector<pair<int,ParkingSpotType>> floorToSpot, string pLotDesc)
	{
		maxFloors = floors;
		parkedVehicle = 0;
		parkingLotDescription = pLotDesc;

		for(auto &itr: floorToSpot){
			capacity = capacity + itr.first;
			mParkingFloor.push_back(new ParkingFloor(itr.first, itr.second));
		}

	}	

	bool ParkVehicle(Vehicle* vehicleArg, ParkingSpotType spotType)
	{

		if(parkedVehicle == capacity)
			displayFull();

		else{
			for(auto &itr: mParkingFloor)
			{
				if(itr->ParkVehicle(vehicleArg, spotType) == true){
					parkedVehicle++;
					return true;
				}
			}
		}

		return false;
	}
	
	void ParkingLotName(){
	    cout<<"====================================\n"
	    <<parkingLotDescription
	    <<"====================================\n";
	}
	
	bool UnparkVehicle(Vehicle* vehicleArg, ParkingSpotType spotType)
	{

		for(auto &itr: mParkingFloor)
		{
			if(itr->UnparkVehicle(vehicleArg, spotType) == true){
				parkedVehicle--;
				return true;
			}
		}

		return false;
	}

	int ParkingLotCapacity() {
		return capacity;
	}

	int AvailableSpots(){
		return (capacity - parkedVehicle);
	}

	void displayFull(){ 
		cout<<"Parking Lot is FULL"<<endl;
	}

	int FreeSpotsForVehicle(ParkingSpotType spotType)
	{
		for(auto &itr: mParkingFloor)
		{
			if(itr->getSpotType() == spotType)
				return itr->getFreeSpots();
		}

		return 0;
	}

};


int main()
{

	int floors = 4;
	string parkingLotDesc = "\t City Center Parking \n\t36th main road, New York\n";
	vector<pair<int,ParkingSpotType>> floorToSpot = {
		{5, Bike},
		{2, Car},
		{5, Van}, 
		{5, Truck}
	};
	
	ParkingLotSystem *mParkingLot = new ParkingLotSystem(floors, floorToSpot, parkingLotDesc);
	
    mParkingLot->ParkingLotName();
	cout<<"Parking lot capacity is: "<<mParkingLot->ParkingLotCapacity()<<endl;
    
    Vehicle *ka01 = new Vehicle("KA01");
    Vehicle *ka02 = new Vehicle("KA02");
    Vehicle *ka03 = new Vehicle("KA03");
    Vehicle *ka04 = new Vehicle("KA04");
    
    mParkingLot->ParkVehicle(ka01, Car);
    mParkingLot->ParkVehicle(ka02, Car);
    mParkingLot->ParkVehicle(ka03, Car);
    mParkingLot->ParkVehicle(ka04, Car);
    
    cout<<"Available spots in Parking lot are: "<<mParkingLot->AvailableSpots()<<endl;
    
    cout<<"Available spots for Parking Car is:"<<mParkingLot->FreeSpotsForVehicle(Car)<<endl;
    
    mParkingLot->UnparkVehicle(ka03, Car);
    mParkingLot->ParkVehicle(ka04, Car);

    return 0;
}

```
