# logistics-shipment-sql-analysis
SQL analysis on logistics shipment data to explore delivery delays, carrier efficiency, and cost optimization.


-- Data Cleaning

SELECT *
FROM logistics_shipments_dataset
;


SELECT 
shipment_id,
COUNT(*) AS duplicate_count
FROM logistics_shipments_dataset
GROUP BY shipment_id
HAVING COUNT(*) > 1;



-- NULL Values

SELECT *
FROM logistics_shipments_dataset
WHERE shipment_id IS NULL
OR delivery_date IS NULL
OR carrier IS NULL
OR origin_Warehouse IS NULL
OR destination IS NULL;
   
   
-- Data Summary


-- Total Shipments
SELECT COUNT(*) AS total_shipments
FROM logistics_shipments_dataset
;

--  Shipment Status

SELECT 
`Status`,
COUNT(*) AS total_shipments,
ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) AS percentage
FROM logistics_shipments_dataset
GROUP BY `Status`;

-- Average Delivery Timeframe
SELECT 
ROUND(AVG(DATEDIFF(delivery_date, shipment_date)), 2) AS avg_delivery_days
FROM logistics_shipments_dataset
WHERE Status = 'Delivered';


-- Average cost by carrier

SELECT 
Carrier,
ROUND(AVG(cost), 2) AS avg_cost
FROM logistics_shipments_dataset
GROUP BY Carrier;

-- Delays per region

SELECT 
destination,
COUNT(*) AS delayed_shipments
FROM logistics_shipments_dataset
WHERE `status` = 'Delayed'
GROUP BY destination
ORDER BY delayed_shipments DESC;

-- Carriers ranked be ontime delivery

SELECT 
carrier,
COUNT(*) AS on_time_deliveries,
RANK() OVER (ORDER BY COUNT(*) DESC) AS driver_rank
FROM logistics_shipments_dataset
WHERE `status` = 'Delivered'
GROUP BY carrier;

-- Shipment cost per distance (in miles)

SELECT 
ROUND(AVG(cost/ NULLIF(Distance_miles, 0)), 2) AS avg_cost_per_mi
FROM logistics_shipments_dataset
GROUP BY carrier;
