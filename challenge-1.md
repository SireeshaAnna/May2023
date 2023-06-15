
#include <stdint.h>

#define MAX_PACKET_DATA_LENGTH (50)

typedef struct data_packet_t {
    uint8_t id;
    uint8_t data_length;
    uint8_t data[MAX_PACKET_DATA_LENGTH];
    uint16_t crc;
} data_packet_t;

// Function to calculate the CRC checksum
uint16_t calculate_crc(const data_packet_t* packet) {
    uint16_t crc = 0;
    uint8_t* packet_data = (uint8_t*)packet;
    uint16_t packet_size = sizeof(data_packet_t) - sizeof(uint16_t);

    // CRC algorithm implementation
    for (uint16_t i = 0; i < packet_size; ++i) {
        crc ^= (uint16_t)(packet_data[i] << 8);
        for (uint8_t j = 0; j < 8; ++j) {
            if (crc & 0x8000)
                crc = (crc << 1) ^ 0x1021;
            else
                crc <<= 1;
        }
    }

    return crc;
}

// Function to check if the data packet is corrupted
bool is_packet_corrupted(const data_packet_t* packet) {
    uint16_t calculated_crc = calculate_crc(packet);
    return (calculated_crc != packet->crc);
}
